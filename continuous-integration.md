---
title: Continuous Integration
description: Up until now, we have only discussed running tests from the command line on your local machine. But, you can also run your tests from a CI platform of your choice. As `pestphp/pest` is included in your Composer development dependencies, you can easily execute the `vendor/bin/pest` command within your CI platform's deployment pipeline.
---

# Continuous Integration

Up until now, we have only discussed running tests from the command line on your local machine. But, you can also run your tests from a CI platform of your choice. As `pestphp/pest` is included in your Composer development dependencies, you can easily execute the `vendor/bin/pest` command within your CI platform's deployment pipeline.

## Example With GitHub Actions

If your application uses [GitHub Actions](https://github.com/features/actions) as its CI platform, the following guidelines will assist you in configuring Pest so that your application is automatically tested when someone pushes a commit to your GitHub repository.

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
          php-version: 8.2
          tools: composer:v2
          coverage: xdebug

      - name: Install Dependencies
        run: composer install --no-interaction --prefer-dist --optimize-autoloader

      - name: Tests
        run: ./vendor/bin/pest
```

Naturally, you may customize the script above according to your requirements. For example, you may need to set up a database if your tests require one.

Once you have created your `tests.yml` file, commit and push the `tests.yml` file so GitHub Actions can run your tests. Keep in mind that once you make this commit, your test suite will execute on all new pull requests and commits.


## Example With GitLab CI/CD Pipelines

If your application uses [GitLab CI/CD Pipelines](https://docs.gitlab.com/ee/ci/pipelines/) as its CI platform, the following guidelines will assist you in configuring Pest so that your application is automatically tested when someone pushes a commit to your GitLab repository.

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
    - ./vendor/bin/pest
```

Naturally, you may customize the script above according to your requirements. For example, you may need to set up a database if your tests require one.

Once you have created your `.gitlab-ci.yml` file, commit and push the `.gitlab-ci.yml` file so Gitlab CI/CD Pipelines can run your tests. Keep in mind that once you make this commit, your test suite will execute on all new merge requests and commits.

## Example with Bitbucket Pipelines

If your application uses [Bitbucket CI/CD Pipelines](https://bitbucket.org/product/features/pipelines) as its CI platform, the following guidelines will assist you in configuring Pest so that your application is automatically tested when someone pushes a commit to your GitLab repository.

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

Naturally, you may customize the script above according to your requirements. For example, you may need to set up a database if your tests require one.

Once you have created your `bitbucket-pipelines.yml` file, commit and push the `bitbucket-pipelines.yml` file so Bitbucket Pipelines can run your tests. Keep in mind that once you make this commit, your test suite will execute on all new pull requests and commits.

## Example with Chipper CI

If your application uses [Chipper CI](https://chipperci.com) as its CI platform, the following guidelines will assist you in configuring Pest so that your application is automatically tested when someone pushes a commit to your git repository.

To get started, add the following configuration to your `.chipperci.yml` file. The file should have the following contents:

```yaml
version: 1

environment:
  php: 8.2
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

In addition to handling Composer and NPM caches, Chipper CI automatically adds `vendor/bin` to your PATH, so simply running the `pest` command will work when running tests.

Naturally, you may customize the scripts above according to your requirements. For example, you may need to define a [database service](https://chipperci.com/docs/builds/databases/) if your tests require one.

Once you have created your `.chipperci.yml` file, commit and push the `.chipperci.yml` file so Chipper CI can run your tests. Keep in mind that once you make this commit, your test suite will execute on all new commits.

---

Great job setting up Continuous Integration for your project to ensure codebase stability! Now, let's take a deeper dive into Pest's concepts by exploring it's test configuration capabilities: [Configuring Pest →](/docs/configuring-tests)
