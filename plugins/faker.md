---
title: Faker Plugin
description: The Faker Plugin
extends: _layouts.documentation
section: content
---

# Faker Plugin

- [Overview](#overview)
- [Installation](#installation)
- [Available Functions](#available-functions)

<a name="overview"></a>
## Overview

The Faker Plugin for Pest provides additional functions for using the [Faker](https://github.com/FakerPHP/Faker) library.

**Source code**: [github.com/pestphp/pest-plugin-faker](https://github.com/pestphp/pest-plugin-faker)

<a name="installation"></a>
## Installation

Install the Faker Plugin via the Composer package manager:

```bash
composer require pestphp/pest-plugin-faker --dev
```

<a name="available-functions"></a>
## Available functions

<div class="collection-method-list" markdown="1">

- [`faker()`](#faker)

</div>

<a name="fake"></a>
### `fake()`

The `fake()` function will create an instance of the Faker generator with the default locale (*en_US*).

```php
use function Pest\Faker\fake;

it('generates a name using faker', function () {
    $name = fake()->name;

    // Same as:
    $name = $this->fake()->name;

    assertIsString($name);
});
```

The `fake()` function also allows you to specify the locale that is used when
creating the instance of the Faker generator.

```php
use function Pest\Faker\fake;

it('generates a name using faker with locale', function () {
    $name = fake('fr_FR')->name;

    assertIsString($name);
});
```

Finally, for the full list of available Faker methods, please refer to the [Faker documentation](https://fakerphp.github.io).

---

Next section: [Global Assertions →](/docs/plugins/global-assertions)
