---
title: Installation
description: Installation
---

# Installation

> **Requirements:** [PHP 7.3+](https://php.net/releases/).

1. First, install Pest via the [Composer](https://getcomposer.org) package manager:

```bash
composer require pestphp/pest --dev --with-all-dependencies
```

2. **On Laravel**, require the `pest-plugin-laravel` and run the `pest:install` _Artisan_ command:

```bash
composer require pestphp/pest-plugin-laravel --dev
php artisan pest:install
```

3. **On other projects**, run the `pest --init` command:

```bash
./vendor/bin/pest --init
```

4. Finally, you can run Pest directly from the command line:

```bash
./vendor/bin/pest
```

![Install](/assets/img/pestinstall.png)

On the next section, we are going to learn how to write tests with Pest: [Writing Tests →](/docs/writing-tests)
