---
title: Continuous Integration
description: Up until now, we have only discussed running test projects from the local command line. Nonetheless, you can also perform testing from a CI platform of your preference. As `pestphp/pest` is included in your Composer development dependencies, you can easily execute the `/vendor/bin/pest` command within your CI platform's deployment pipeline.
---

# Continuous Integration

Up until now, we have only discussed running test projects from the local command line. Nonetheless, you can also perform testing from a CI platform of your preference. As `pestphp/pest` is included in your Composer development dependencies, you can easily execute the `/vendor/bin/pest` command within your CI platform's deployment pipeline.

## Example With GitHub Actions

If your application uses [GitHub Actions](https://github.com/features/actions) as its CI platform, the following guidelines will assist you in configuring Pest so that your application is automatically tested when someone pushes a commit.

1. Next, create a `tests.yml` file within the `your-project/.github/workflows` directory. The file should have the following contents:

> Note: Naturally, you should customize the script above according to your requirements. For instance, if you are not utilizing Laravel, then exclude the "Prepare Laravel" step. Similarly, if you do not utilize "npm" at all, then remove any "npm" related steps.

```yml
name: Tests

on: ['push', 'pull_request']

jobs:
  ci:
    runs-on: ubuntu-22.04
    strategy:
      fail-fast: true
      matrix:
        php: [8.2]
        composer-flags:
          - '--prefer-stable'

    name: Tests

    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Cache Dependencies
        uses: actions/cache@v1
        with:
          path: ~/.composer/cache/files
          key: dependencies-php-composer-${{ hashFiles('composer.lock') }}

      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: ${{ matrix.php }}
          extensions: json, dom, curl, libxml, mbstring, zip
          tools: composer:v2
          coverage: none

      - name: Set up Node & NPM
        uses: actions/setup-node@v2
        with:
          node-version: '16.x'

      - name: Setup Problem Matches
        run: |
          echo "::add-matcher::${{ runner.tool_cache }}/php.json"
          echo "::add-matcher::${{ runner.tool_cache }}/phpunit.json"

      - name: Install PHP dependencies
        run: composer install --no-interaction --no-progress --ansi

      - name: Cache dependencies
        id: cache
        uses: actions/cache@v2
        with:
          path: ./node_modules
          key: modules-${{ hashFiles('package-lock.json') }}

      - name: Install dependencies
        if: steps.cache.outputs.cache-hit != 'true'
        run: npm install

      - name: Build dependencies
        run: npm run build

      - name: Prepare Laravel
        run: |
          cp .env.example .env
          php artisan key:generate

      - name: Tests
        run: ./vendor/bin/pest --ci
```

2. Once you are done, commit and push the `tests.yml` file so GitHub Actions can run the first tests job.

---

Great job setting up Continuous Integration for your project to ensure codebase stability! Now, let's take a deeper dive into Pest's concepts by exploring it's configure file: [Configuring Pest →](/docs/configuring-pest)
