---
title: Installation
description: Installation
---

# Installation

- [Requirements](#requirements)
- [Install Pest](#install-pest)
- [Setup the test suite](#setup-the-test-suite)
- [Run Pest](#run-pest)


<a name="requirements"></a>
## Requirements

> **Note:** Pest requires [PHP 7.3+](https://php.net/releases/) to work.

1. Check that you have a `phpunit.xml` file at the root of your project. Otherwise, [download this one](https://github.com/pestphp/pest/blob/master/stubs/Laravel/phpunit.xml).

2. Make sure your existing `composer.json` file contains the following options:

```json
"minimum-stability": "dev",
"prefer-stable": true,
```

3. Then, make sure your _PHPUnit_ dependency is set to `^9.3.10`:

```bash
composer require phpunit/phpunit:"^9.3.10" --dev --update-with-dependencies
```

**If you are using Laravel**, make sure your _Collision_ dependency is set to `^5.0`:

```bash
composer require nunomaduro/collision:"^5.0" --dev --update-with-dependencies
```

<a name="install-pest"></a>
## Install Pest

Now you can require **Pest**:

```bash
composer require pestphp/pest --dev
```

<a name="setup-the-test-suite"></a>
## Setup the test suite

Now, it's time to setup your Pest test suite.

- **On Laravel**, require the `pest-plugin-laravel` and run the `pest:install` _Artisan_ command:

```bash
composer require pestphp/pest-plugin-laravel --dev && php artisan pest:install
```

- **On other projects**, run the `pest --init` command:

```bash
./vendor/bin/pest --init
```

<a name="run-pest"></a>
## Run Pest


Finally, you can run Pest directly from the command line:

```bash
./vendor/bin/pest
```

![Install](/assets/img/pestinstall.png)

On the next section, we are going to learn how to write tests with Pest: [Writing Tests →](/docs/writing-tests)
