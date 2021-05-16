---
title: Mock Plugin
description: The Mock Plugin
---

# Mock Plugin

- [Overview](#overview)
- [Installation](#installation)
- [Available Functions](#available-functions)
- [Available Functions](#available-functions)

<a name="overview"></a>
## Overview

The Mock Plugin for Pest adds mocking capabilities to Pest - it's a very simple API built on top of **[Mockery](https://github.com/mockery/mockery)**.

In unit tests, mock objects simulate the behaviour of real objects. They are commonly utilised to offer test isolation, to stand in for objects which do not yet exist, or to allow for the exploratory design of class APIs without requiring actual implementation up front.

**Source code**: [github.com/pestphp/pest-plugin-mock](https://github.com/pestphp/pest-plugin-mock)

<a name="installation"></a>
### Installation

Install the Mock Plugin via the Composer package manager:

```bash
composer require pestphp/pest-plugin-mock --dev
```

<a name="available-functions"></a>
## Available functions

<div class="collection-method-list" markdown="1">

- [`mock()`](#mock)

</div>

<a name="mock"></a>
### `mock()`

The `mock()` function allows you to declare methods call expectations:

```php
test('some service', function () {
    $mock = mock(UserRepository::class)->expect(
        create: fn ($name) => false,
        save: fn ($name) => true,
    );

    expect($mock->create('Nuno'))->toBeFalse();
    expect($mock->save('Nuno'))->toBeTrue();
});
```

When using the `expect(callable ...$methods)` method, you will be creating an expectation that each given method should be called at least once and return the given callable result.

Of course, while the `expect(callable ...$methods)` method addresses most of the tradicional mocking needs, you may have more complex cases that require to use the underyling Mockery mock instance:

```php
test('some service', function () {
    $mock = mock(UserRepository::class)
        ->shouldReceive('save')
        ->once
        ->andReturn(true);

    expect($mock->save('Nuno'))->toBeTrue();
});
```

For the full list of available methods, please refer to the **[Mockery documentation](http://docs.mockery.io/en/latest/)**.

---

Next section: [Mutation Testing Plugin →](/docs/plugins/mutation-testing)
