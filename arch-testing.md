---
title: Arch Testing
description: The Arch Plugin
---

# Arch Testing

Architectural testing enables you to specify expectations for different sections of your codebase and test whether certain components adhere to architectural rules. This process verifies that specific dependencies are being used by particular components and helps maintain a modular and sustainable codebase.

<div class="collection-method-list" markdown="1">

- [`toOnlyUse()`](#expect-toOnlyUse)
- [`toOnlyBeUsedOn()`](#expect-toOnlyBeUsedOn)
- [`toBeUsed()`](#expect-toBeUsed)
- [`toBeUsedOn()`](#expect-toBeUsedOn)
- [`toUse()`](#expect-toUse)
- [`toUseNothing()`](#expect-toUseNothing)

</div>

<a name="expect-toOnlyUse"></a>
### `toOnlyUse()`

For instance, you can utilize the `toOnlyUse()` expectation to ensure that your models are compact and dependent solely on the `Illuminate\Database` namespace, without dispatching queue jobs or events.

```php
test('models')
    ->expect('App\Models')
    ->toOnlyUse('Illuminate\Database');
```

<a name="expect-toOnlyBeUsedOn"></a>
### `toOnlyBeUsedOn()`

Furthermore, with the method `toOnlyBeUsedOn`, you can confirm that your models are only used by your repositories and not directly by controllers or service providers.

```php
test('models')
    ->expect('App\Models')
    ->toOnlyBeUsedOn('App\Repositories');
```

<a name="expect-toBeUsed"></a>
### `toBeUsed()`

The `not` modifier, when combined with the `toBeUsed` method, enables you to verify whether certain layers, functions, or classes are not being utilized.

```php
test('globals')
    ->expect(['dd', 'dump'])
    ->not->toBeUsed();

test('facades')
    ->expect('Illuminate\Support\Facades')
    ->not->toBeUsed();
```

<a name="expect-toBeUsedOn"></a>
### `toBeUsedOn()`

At times, you may desire to restrict certain layers, functions, or classes in specific layers. In such cases, you can utilize the `not` modifier along with the `toBeUsedOn` method.

```php
test('globals')
    ->expect('request')
    ->not->toBeUsedOn('App\Domain');

test('globals')
    ->expect('Illuminate\Http')
    ->not->toBeUsedOn('App\Domain');
```

<a name="expect-toUse"></a>
### `toUse()`

You can also use the `toUse` method as a substitute for `toBeUsedOn` to indicate whether a specific layer should not use a particular layer, function, or class.

```php
test('globals')
    ->expect('App\Domain')
    ->not->toUse('request');

test('globals')
    ->expect('App\Domain')
    ->not->toUse('Illuminate\Http');
```

<a name="expect-toUseNothing"></a>
### `toUseNothing()`

If you want to indicate that particular layers or classes should not have any dependencies, you can utilize the `toUseNothing` method.

```php
test('value objects')
    ->expect('App\ValueObjects')
    ->toUseNothing();
```

---

In this section, you have learned how to perform architectural testing, ensuring that your application or library's architecture meets the specified architectural requirements. Moving on, let's explore how to create custom helpers on Pest: [Custom Helpers](/docs/custom-helpers)
