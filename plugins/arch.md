---
title: Arch Plugin
description: The Arch Plugin
extends: _layouts.documentation
section: content
---

# Arch Plugin

- [Overview](#overview)
- [Installation](#installation)
- [Available Expectations](#available-expectations)

<a name="overview"></a>
## Overview

The Arch Plugin for Pest allows you to test the architectural rules of your application by specifying expectations for different parts of your codebase. With this plugin, you can run tests on your codebase to verify that certain components are only using specific dependencies and are only being used by certain other components. This helps to maintain a modular, maintainable codebase.

For example, you can use this plugin to test that your models are small and only depend on the “Illuminate\Database” namespace, ensuring that they don’t dispatch queue jobs or events. You can also ensure they are only being used by your repositories.

```php
test('models')
    ->expect('App\Models')
    ->toOnlyUse('Illuminate\Database')
    ->toOnlyBeUsedOn('App\Repositories');
```

In addition, you can ensure that your repositories are only using your models and are only being used by your controllers.

```php
test('repositories')
    ->expect('App\Repositories')
    ->toOnlyUse('App\Models')
    ->toOnlyBeUsedOn('App\Http\Controllers');
```

You can also test that certain global functions or facades are not being used, with the ability to ignore certain components.

```php
test('globals')
    ->expect(['dd', 'dump'])
    ->each->not->toBeUsed();

test('facades')
    ->expect('Illuminate\Support\Facades')
    ->not->toBeUsed()
    ->ignoring('App\Providers');
```

You can even specify, that Value Objects in your application don't have any dependency:

```php
test('value objects')
    ->expect('App\ValueObjects')
    ->toUseNothing();
```

**Source code**: [github.com/pestphp/pest-plugin-arch](https://github.com/pestphp/pest-plugin-arch)

<a name="installation"></a>
## Installation

Install the Arch Plugin via the Composer package manager:



```bash
composer require pestphp/pest-plugin-arch --dev
```

<a name="available-functions"></a>
## Available functions

<div class="collection-method-list" markdown="1">

- [`arch()`](#arch)

</div>

<a name="arch"></a>
### `arch()`

The `arch()` function will create an instance of the Arch generator with the default locale (*en_US*).

```php
use function Pest\Arch\arch;

it('generates a name using arch', function () {
    $name = arch()->name;

    // Same as:
    $name = $this->arch()->name;

    assertIsString($name);
});
```

The `arch()` function also allows you to specify the locale that is used when
creating the instance of the Arch generator.

```php
use function Pest\Arch\arch;

it('generates a name using arch with locale', function () {
    $name = arch('fr_FR')->name;

    assertIsString($name);
});
```

Finally, for the full list of available Arch methods, please refer to the [Arch documentation](https://archphp.github.io).

---

Next section: [Global Assertions →](/docs/plugins/laravel)