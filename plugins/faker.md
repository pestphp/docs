---
title: Faker Plugin
description: The Faker Plugin
extends: _layouts.documentation
section: content
---

# Faker Plugin

- [Overview](#overview)
	- [Installation](#installation)
- [Usage](#usage)
    - [`faker()`](#faker)

<a name="overview"></a>
## Overview

The Faker Plugin for Pest provides additional functions for using the [Faker](https://github.com/FakerPHP/Faker) library.

**Source code**: [github.com/pestphp/pest-plugin-faker](https://github.com/pestphp/pest-plugin-faker)

<a name="install"></a>
## Install

Install the plugin using Composer:

```bash
composer require pestphp/pest-plugin-faker --dev
```

<a name="usage"></a>
## Usage

<a name="faker"></a>
### `faker()`

The `faker()` function will create an instance of the Faker generator with the default locale (*en_US*).

```php
use function Pest\Faker\faker;

it('generates a name using faker', function () {
    $name = faker()->name;
    
    assertIsString($name);
});
```

The `faker()` function also allows you to specify the locale that is used when
creating the instance of the Faker generator.

```php
use function Pest\Faker\faker;

it('generates a name using faker with locale', function () {
    $name = faker('fr_FR')->name;
    
    assertIsString($name);
});
```

Finally, for the full list of available Faker methods, please refer to the [Faker documentation](https://github.com/FakerPHP/Faker#formatters).

Next section: [Global Assertions →](/docs/plugins/global-assertions)
