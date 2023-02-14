---
title: Installation
description: Installation
---

# Installation

- [Requirements](#requirements)
- [Install Pest](#install-pest)

<a name="requirements"></a>

## Requirements

- [PHP 7.3+](https://php.net/releases/)
- [Composer](https://getcomposer.org/)
- [PHPUnit 9.6.x](https://github.com/sebastianbergmann/phpunit)
- [Collision 6.3.x](https://github.com/nunomaduro/collision)

> ❗ **IMPORTANT**: To install Pest v1 in a **Laravel 10** project, you must downgrade PHPUnit and Collision to compatible versions.<br/>Run the  following command:
> 
>```bash
>composer require phpunit/phpunit:^9.6 nunomaduro/collision:^6.1 --dev --with-all-dependencies
>```

<a name="install-pest"></a>

## Install

The installation process consists of 3 steps:

``- Step 1:`` Require Pest as a dev dependency in your project. Run:

```bash
composer require pestphp/pest --dev --with-all-dependencies
```

<hr/>

`- Step 2:` Continue the installation according to your project specification:

`Laravel Framework:`

First, you must require the [Laravel Plugin](/docs/plugins/laravel). Run the following command:

  ```bash
  composer require pestphp/pest-plugin-laravel --dev
  ```

Now, install Pest using Artisan. Run the command below:

  ```bash
  php artisan pest:install
  ```

<br/>

`Other PHP Projects:`

Initialize Pest, run the following command:

```bash
./vendor/bin/pest --init
```

---

`- Step 3:` To run your tests, execute the command:

```bash
./vendor/bin/pest
```

<br/>

The image below shows the default Test output in a brand-new project:

![Install](/assets/img/pestinstall.png)

---

**Next**: In the next section, we are going to learn how to write tests with Pest: [Writing Tests →](docs/writing-tests)
