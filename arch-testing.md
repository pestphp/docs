---
title: Architecture Testing
description: Architecture testing enables you to specify expectations that test whether your application adheres to a set of architectural rules, helping you maintain a clean and sustainable codebase.
---

# Architecture Testing

Architecture testing enables you to specify expectations that test whether your application adheres to a set of architectural rules, helping you maintain a clean and sustainable codebase. The expectations are determined by either Relative namespaces, fully qualified namespaces, or function names.

<div class="collection-method-list" markdown="1">

- [`toOnlyUse()`](#expect-toOnlyUse)
- [`toOnlyBeUsedIn()`](#expect-toOnlyBeUsedIn)
- [`toBeUsed()`](#expect-toBeUsed)
- [`toBeUsedIn()`](#expect-toBeUsedIn)
- [`toUse()`](#expect-toUse)
- [`toUseNothing()`](#expect-toUseNothing)
- [`ignoring()`](#expect-ignoring)

</div>

<a name="expect-toOnlyUse"></a>
### `toOnlyUse()`

The `toOnlyUse()` method may be used to guarantee that certain classes are restricted to utilizing specific functions or classes. For example, you may ensure your models are streamlined and solely dependent on the `Illuminate\Database` namespace, and not, for instance, dispatching queued jobs or events.

```php
test('models')
    ->expect('App\Models')
    ->toOnlyUse('Illuminate\Database');
```

<a name="expect-toOnlyBeUsedIn"></a>
### `toOnlyBeUsedIn()`

The `toOnlyBeUsedIn()` method enables you to limit the usage of a specific class or set of classes to only particular parts of your application. For instance, you can use this method to confirm that your models are only used by your repositories and not by controllers or service providers.

```php
test('models')
    ->expect('App\Models')
    ->toOnlyBeUsedIn('App\Repositories');
```

<a name="expect-toBeUsed"></a>
### `toBeUsed()`

The `not` modifier, when combined with the `toBeUsed()` method, enables you to verify that certain classes or functions are not being utilized by your application.

```php
test('globals')
    ->expect(['dd', 'dump'])
    ->not->toBeUsed();

test('facades')
    ->expect('Illuminate\Support\Facades')
    ->not->toBeUsed();
```

<a name="expect-toBeUsedIn"></a>
### `toBeUsedIn()`

By combining the `not` modifier with the `toBeUsedIn()` method, you can restrict specific classes and functions from being used within a given namespace.

```php
test('globals')
    ->expect('request')
    ->not->toBeUsedIn('App\Domain');

test('globals')
    ->expect('Illuminate\Http')
    ->not->toBeUsedIn('App\Domain');
```

<a name="expect-toUse"></a>
### `toUse()`

By combining the `not` modifier with the `toUse()` method, you can indicate that files within a given namespace should not use specific functions or classes.

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

If you want to indicate that particular namespaces or classes should not have any dependencies, you can utilize the `toUseNothing()` method.

```php
test('value objects')
    ->expect('App\ValueObjects')
    ->toUseNothing();
```

<a name="expect-ignoring"></a>
### `ignoring()`

When defining your architecture rules, you can use the `ignoring()` method to exclude certain namespaces or classes that would otherwise be included in the rule definition.

```php
test('facades')
    ->expect('Illuminate\Support\Facades')
    ->not->toBeUsed()
    ->ignoring('App\Providers');
```

In some cases, certain components may not be regarded as "dependencies" as they are part of the native PHP library. To customize the definition of "native" code and exclude it during testing, Pest allows you to specify what to ignore.

For example, if you do not want to consider Laravel a "dependency", you can use the `arch()` method inside the `beforeEach()` function to disregard any code within the "Illuminate" namespace. This approach allows you to focus only on the actual dependencies of your application.

```php
// tests/Pest.php
uses()->beforeEach(function () {
    $this->arch()->ignore([
        'Illuminate',
    ])->ignoreGlobalFunctions();
})->in('Feature');
```

---

In this section, you have learned how to perform architectural testing, ensuring that your application or library's architecture meets a specified set of architectural requirements. Moving on, let's explore how to test the coverage of your testing code: [Test Coverage](/docs/test-coverage)
