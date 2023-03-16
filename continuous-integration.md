---
title: Continuous Integration
description: Up until now, we have only discussed running tests from the command line on your local machine. But, you can also run your tests from a CI platform of your choice. As `pestphp/pest` is included in your Composer development dependencies, you can easily execute the `/vendor/bin/pest` command within your CI platform's deployment pipeline.
---

# Continuous Integration

Up until now, we have only discussed running tests from the command line on your local machine. But, you can also run your tests from a CI platform of your choice. As `pestphp/pest` is included in your Composer development dependencies, you can easily execute the `/vendor/bin/pest` command within your CI platform's deployment pipeline.

## Example With GitHub Actions

If your application uses [GitHub Actions](https://github.com/features/actions) as its CI platform, the following guidelines will assist you in configuring Pest so that your application is automatically tested when someone pushes a commit to your GitHub repository.

To get started, create a `tests.yml` file within the `your-project/.github/workflows` directory. The file should have the following contents:

```yml
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

---

Great job setting up Continuous Integration for your project to ensure codebase stability! Now, let's take a deeper dive into Pest's concepts by exploring it's test configuration capabilities: [Configuring Pest →](/docs/configuring-tests)
