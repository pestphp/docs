---
title: Continuous Integration
description: Since `pestphp/pest` is included in your Composer development dependencies, you may run your test suite within the deployment pipeline of the CI platform of your choice.
---

# Continuous Integration

Up until now, we have only discussed running tests from the command line on your local machine. However, you may also run your tests from a CI platform of your choice. Since `pestphp/pest` is included in your Composer development dependencies, you may execute the `./vendor/bin/pest --ci` command within your CI platform's deployment pipeline.

## Example With GitHub Actions

If your application uses [GitHub Actions](https://github.com/features/actions) as its CI platform, the following guidelines will help you configure Pest so that your application is automatically tested whenever someone pushes a commit to your GitHub repository.

To get started, create a `tests.yml` file within the `your-project/.github/workflows` directory. The file should have the following contents:

```yaml
name: Tests

on: ['push', 'pull_request']

jobs:
  ci:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: 8.4
          tools: composer:v2
          coverage: xdebug

      - name: Install Dependencies
        run: composer install --no-interaction --prefer-dist --optimize-autoloader

      - name: Tests
        run: ./vendor/bin/pest --ci
```

Of course, you may customize the script above according to your requirements. For example, you may need to set up a database if your tests require one.

Once you have created your `tests.yml` file, commit and push the `tests.yml` file so GitHub Actions can run your tests. Keep in mind that once you make this commit, your test suite will execute on all new pull requests and commits.

### Using Browser Testing with GitHub Actions

Sometimes you may wish to use [Browser Testing](/docs/browser-testing) with GitHub Actions. To do so, be sure to add a step that installs [Playwright](https://playwright.dev/docs/ci#github-actions) before running your tests. Here is an example:

```yaml
    - uses: actions/setup-node@v4
      with:
        node-version: lts/*

    - name: Install dependencies
      run: npm ci

    - name: Install Playwright Browsers
      run: npx playwright install --with-deps

    - name: Run Browser Tests
      run: ./vendor/bin/pest --ci --parallel
```

> **Note:** Be sure to run your browser tests in parallel to speed up execution time. You may do this by adding the `--parallel` flag to the Pest command.

## Example With GitLab CI/CD Pipelines

If your application uses [GitLab CI/CD Pipelines](https://docs.gitlab.com/ee/ci/pipelines/) as its CI platform, the following guidelines will help you configure Pest so that your application is automatically tested whenever someone pushes a commit to your GitLab repository.

To get started, add the following configuration to your `.gitlab-ci.yml` file. The file should have the following contents:

```yaml
stages:
  - build
  - test
  
build:vendors:
  stage: build
  only:
    refs:
      - merge_requests
      - push
  cache:
    key:
      files:
        - composer.lock
    policy: pull-push
  image: composer:2
  script:
    - composer install --no-interaction --prefer-dist --optimize-autoloader
      
tests:
  stage: test
  only:
    refs:
      - merge_requests
      - push
  cache:
    key:
      files:
        - composer.lock
    policy: pull
  image: php:8.2
  script:
    - ./vendor/bin/pest --ci
```

Of course, you may customize the script above according to your requirements. For example, you may need to set up a database if your tests require one.

Once you have created your `.gitlab-ci.yml` file, commit and push the `.gitlab-ci.yml` file so GitLab CI/CD Pipelines can run your tests. Keep in mind that once you make this commit, your test suite will execute on all new merge requests and commits.

## Example with Bitbucket Pipelines

If your application uses [Bitbucket CI/CD Pipelines](https://bitbucket.org/product/features/pipelines) as its CI platform, the following guidelines will help you configure Pest so that your application is automatically tested whenever someone pushes a commit to your Bitbucket repository.

To get started, add the following configuration to your `bitbucket-pipelines.yml` file. The file should have the following contents:

```yaml
image: composer:2

pipelines:
  default:
  - parallel:
      - step:
          name: Test
          script:
            - composer install --no-interaction --prefer-dist --optimize-autoloader
            - ./vendor/bin/pest
          caches:
            - composer
```

Of course, you may customize the script above according to your requirements. For example, you may need to set up a database if your tests require one.

Once you have created your `bitbucket-pipelines.yml` file, commit and push the `bitbucket-pipelines.yml` file so Bitbucket Pipelines can run your tests. Keep in mind that once you make this commit, your test suite will execute on all new pull requests and commits.

## Example with Chipper CI

If your application uses [Chipper CI](https://chipperci.com) as its CI platform, the following guidelines will help you configure Pest so that your application is automatically tested whenever someone pushes a commit to your git repository.

To get started, add the following configuration to your `.chipperci.yml` file. The file should have the following contents:

```yaml
version: 1

environment:
  php: 8.4
  node: 16

# Optional services
services:
#  - mysql: 8
#  - redis:

# Build all commits
on:
   push:
      branches: .*

pipeline:
  - name: Setup
    cmd: |
      cp -v .env.example .env
      composer install --no-interaction --prefer-dist --optimize-autoloader
      php artisan key:generate

  - name: Compile Assets
    cmd: |
      npm ci --no-audit
      npm run build

  - name: Test
    cmd: pest
```

In addition to handling Composer and NPM caches, Chipper CI automatically adds `vendor/bin` to your PATH, so running the `pest --ci` command will work when running tests.

Of course, you may customize the scripts above according to your requirements. For example, you may need to define a [database service](https://chipperci.com/docs/builds/databases/) if your tests require one.

Once you have created your `.chipperci.yml` file, commit and push the `.chipperci.yml` file so Chipper CI can run your tests. Keep in mind that once you make this commit, your test suite will execute on all new commits.

## Sharding Your Tests

If you have a large test suite, you may wish to shard your tests across multiple CI jobs to speed up execution time. Pest supports test sharding out of the box, allowing you to split your tests into smaller groups that may be run in parallel.

To shard your tests, you may use the `--shard` option when running Pest. For example, to run the first shard of your tests, you may use the following command:

```bash
./vendor/bin/pest --shard=1/5
```

By default, Pest splits tests evenly by count — each shard gets roughly the same number of test files. This works well when all tests take similar time, but can create imbalanced shards when some tests (like payment processing or report generation) are significantly slower than others.

### Time-Balanced Sharding

For better shard balance, Pest may distribute tests based on their actual execution time using the `--update-shards` option. This ensures each shard takes roughly the same wall-clock time, minimizing how long your slowest CI job runs.

First, generate the timing data by running your full test suite with the `--update-shards` option:

```bash
./vendor/bin/pest --update-shards
```

This runs all tests and records each test class's duration into `tests/.pest/shards.json`. You may also combine it with `--parallel` to speed things up:

```bash
./vendor/bin/pest --parallel --update-shards
```

Next, commit `tests/.pest/shards.json` to your repository. This file is human-readable and looks like this:

```json
{
    "timings": {
        "Tests\\Feature\\Payments\\StripeCheckoutTest": 1.608,
        "Tests\\Feature\\Reports\\SalesReportTest": 2.105,
        "Tests\\Unit\\Models\\UserTest": 0.050
    },
    "checksum": "...",
    "updated_at": "2026-04-14T10:30:00+00:00"
}
```

Finally, when you run `--shard` and `tests/.pest/shards.json` exists, Pest will automatically use time-balanced distribution:

```bash
./vendor/bin/pest --shard=1/5
```

The output will indicate that time-balanced sharding is active:

```plain
Shard:    1 of 5 — 12 files ran, out of 50 (time-balanced).
```

### Keeping Shards Up to Date

When you add or rename test files, Pest will detect that `tests/.pest/shards.json` is out of date. Don't worry — your tests will still run. New test files are distributed evenly across shards, while known tests remain time-balanced. However, Pest will display a warning after the run:

```plain
WARN  The [tests/.pest/shards.json] file is out of date. Run [--update-shards] to update it.
```

Re-run `--update-shards` and commit the updated file to restore optimal balancing.

Here is how Pest handles common changes to your test suite:

- **Adding test files**: Tests run with a warning. New files are distributed across shards, known files stay time-balanced.
- **Deleting test files**: Tests run without a warning. Stale timing entries are harmlessly ignored.
- **Adding tests inside an existing file**: Tests run without a warning. The test class is already known — only its internal timing shifts.
- **Renaming a test file**: Tests run with a warning. The old name is ignored, the new name is treated as a new file.
- **Corrupted `shards.json`**: Pest stops with a clear error asking you to delete it or run `--update-shards` to regenerate.

### GitHub Actions Example

Here is a complete example of time-balanced sharding with GitHub Actions:

```yml
strategy:
  matrix:
    shard: [1, 2, 3, 4, 5]

name: Tests (Shard ${{ matrix.shard }}/5)

steps:
  - name: Run tests
    run: ./vendor/bin/pest --shard=${{ matrix.shard }}/5
```

To refresh timing data, you may add a scheduled or manual workflow:

```yml
name: Update Shards

on:
  workflow_dispatch:
  schedule:
    - cron: '0 0 * * 1' # Weekly on Monday

jobs:
  update-shards:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Update shards.json
        run: ./vendor/bin/pest --parallel --update-shards
      - name: Commit changes
        run: |
          git config user.name "github-actions"
          git config user.email "github-actions@github.com"
          git add tests/.pest/shards.json
          git commit -m "chore: update shards.json" || true
          git push
```

---

With Continuous Integration in place, your project is set up to keep its codebase stable on every commit. Next, let's take a deeper dive into Pest's concepts by exploring its test configuration capabilities: [Configuring Pest →](/docs/configuring-tests)
