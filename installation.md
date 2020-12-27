---
title: Installation
description: Installation
---

# Installation

> **Requirements:** [PHP 7.3+](https://php.net/releases/).

1. First, install Pest via the [Composer](https://getcomposer.org) package manager:

```bash
composer require pestphp/pest --dev --update-with-dependencies
```

2. Check that you have a `phpunit.xml` file at the root of your project. Otherwise, [download this one](https://github.com/pestphp/pest/blob/master/stubs/Laravel/phpunit.xml).

3. **On Laravel**, require the `pest-plugin-laravel` and run the `pest:install` _Artisan_ command:

```bash
composer require pestphp/pest-plugin-laravel --dev && php artisan pest:install
```

4. **On other projects**, run the `pest --init` command:

```bash
./vendor/bin/pest --init
```

5. Finally, you can run Pest directly from the command line:

```bash
./vendor/bin/pest
```

![Install](/assets/img/pestinstall.png)

On the next section, we are going to learn how to write tests with Pest: [Writing Tests →](/docs/writing-tests)
