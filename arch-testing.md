---
title: Architecture Testing
description: Architecture testing enables you to specify expectations that test whether your application adheres to a set of architectural rules, helping you maintain a clean and sustainable codebase.
---

# Architecture Testing

Architecture testing enables you to specify expectations that test whether your application adheres to a set of architectural rules, helping you maintain a clean and sustainable codebase. The expectations are determined by either Relative namespaces, fully qualified namespaces, or function names.

## Expectations

<div class="collection-method-list" markdown="1">

- [`toBeAbstract()`](#expect-toBeAbstract)
- [`toBeClasses()`](#expect-toBeClasses)
- [`toBeEnums()`](#expect-toBeEnums)
- [`toBeInterfaces()`](#expect-toBeInterfaces)
- [`toBeInvokable()`](#expect-toBeInvokable)
- [`toBeFinal()`](#expect-toBeFinal)
- [`toBeReadonly()`](#expect-toBeReadonly)
- [`toBeTraits()`](#expect-toBeTraits)
- [`toBeUsed()`](#expect-toBeUsed)
- [`toBeUsedIn()`](#expect-toBeUsedIn)
- [`toExtend()`](#expect-toExtend)
- [`toExtendNothing()`](#expect-toExtendNothing)
- [`toImplement()`](#expect-toImplement)
- [`toImplementNothing()`](#expect-toImplementNothing)
- [`toHaveAttribute()`](#expect-toHaveAttribute)
- [`toHaveMethod()`](#expect-toHaveMethod)
- [`toHavePrefix()`](#expect-toHavePrefix)
- [`toHaveSuffix()`](#expect-toHaveSuffix)
- [`toHaveConstructor()`](#expect-toHaveConstructor)
- [`toHaveDestructor()`](#expect-toHaveDestructor)
- [`toOnlyImplement()`](#expect-toOnlyImplement)
- [`toOnlyUse()`](#expect-toOnlyUse)
- [`toOnlyBeUsedIn()`](#expect-toOnlyBeUsedIn)
- [`toUse()`](#expect-toUse)
- [`toUseNothing()`](#expect-toUseNothing)
- [`toUseStrictTypes()`](#expect-toUseStrictTypes)

</div>

<a name="expect-toBeAbstract"></a>
### `toBeAbstract()`

The `toBeAbstract()` method may be used to ensure that all classes within a given namespace are abstract.

```php
test('app')
    ->expect('App\Models')
    ->toBeAbstract();
```

<a name="expect-toBeClasses"></a>
### `toBeClasses()`

The `toBeClasses()` method may be used to ensure that all files within a given namespace are classes.

```php
test('app')
    ->expect('App\Models')
    ->toBeClasses();
```

<a name="expect-toBeEnums"></a>
### `toBeEnums()`

The `toBeEnums()` method may be used to ensure that all files within a given namespace are enums.

```php
test('app')
    ->expect('App\Enums')
    ->toBeEnums();
```

<a name="expect-toBeInterfaces"></a>
### `toBeInterfaces()`

The `toBeInterfaces()` method may be used to ensure that all files within a given namespace are interfaces.

```php
test('app')
    ->expect('App\Contracts')
    ->toBeInterfaces();
```

<a name="expect-toBeInvokable"></a>
### `toBeInvokable()`

The `toBeInvokable()` method may be used to ensure that all files within a given namespace are invokable.

```php
test('app')
    ->expect('App\Actions')
    ->toBeInvokable();
```

<a name="expect-toBeTraits"></a>
### `toBeTraits()`

The `toBeTraits()` method may be used to ensure that all files within a given namespace are traits.

```php
test('app')
    ->expect('App\Concerns')
    ->toBeTraits();
```

<a name="expect-toBeFinal"></a>
### `toBeFinal()`

The `toBeFinal()` method may be used to ensure that all classes within a given namespace are final.

```php
test('app')
    ->expect('App\ValueObjects')
    ->toBeFinal();
```

Note that, typically this expectation is used in combination with the `classes()` modifier to ensure that all classes within a given namespace are final.

```php
test('app')
    ->expect('App')
    ->classes()
    ->toBeFinal();
```

<a name="expect-toBeReadonly"></a>
### `toBeReadonly()`

The `toBeReadonly()` method may be used to ensure that certain classes are immutable and cannot be modified at runtime.

```php
test('app')
    ->expect('App\ValueObjects')
    ->toBeReadonly();
```

Note that, typically this expectation is used in combination with the `classes()` modifier to ensure that all classes within a given namespace are readonly.

```php
test('app')
    ->expect('App')
    ->classes()
    ->toBeReadonly();
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

<a name="expect-toExtend"></a>
### `toExtend()`

The `toExtend()` method may be used to ensure that all classes within a given namespace extend a specific class.

```php
test('app')
    ->expect('App\Models')
    ->toExtend('Illuminate\Database\Eloquent\Model');
```

<a name="expect-toExtendNothing"></a>
### `toExtendNothing()`

The `toExtendNothing()` method may be used to ensure that all classes within a given namespace do not extend any class.

```php
test('app')
    ->expect('App\ValueObjects')
    ->toExtendNothing();
```

<a name="expect-toImplement"></a>
### `toImplement()`

The `toImplement()` method may be used to ensure that all classes within a given namespace implement a specific interface.

```php
test('app')
    ->expect('App\Jobs')
    ->toImplement('Illuminate\Contracts\Queue\ShouldQueue');
```

<a name="expect-toImplementNothing"></a>
### `toImplementNothing()`

The `toImplementNothing()` method may be used to ensure that all classes within a given namespace do not implement any interface.

```php
test('app')
    ->expect('App\ValueObjects')
    ->toImplementNothing();
```

<a name="expect-toHaveAttribute"></a>
### `toHaveAttribute()`

The `toHaveAttribute()` method may be used to ensure that a certain class has a specific attribute.

```php
test('app')
    ->expect('App\Console\Commands')
    ->toHaveAttribute('Symfony\Component\Console\Attribute\AsCommand');
```

<a name="expect-toHaveMethod"></a>
### `toHaveMethod()`

The `toHaveMethod()` method may be used to ensure that a certain class has a specific method.

```php
test('app')
    ->expect('App\Http\Controllers\HomeController')
    ->toHaveMethod('index');
```

<a name="expect-toHavePrefix"></a>
### `toHavePrefix()`

The `toHavePrefix()` method may be used to ensure that all files within a given namespace have a specific prefix.

```php
test('app')
    ->expect('App\Helpers')
    ->not->toHavePrefix('Helper');
```

<a name="expect-toHaveSuffix"></a>
### `toHaveSuffix()`

The `toHaveSuffix()` method may be used to ensure that all files within a given namespace have a specific suffix.

```php
test('app')
    ->expect('App\Http\Controllers')
    ->toHaveSuffix('Controller');
```

<a name="expect-toHaveConstructor"></a>
### `toHaveConstructor()`

This `toHaveConstructor()` method may be used to ensure that all files within a given namespace have a `__construct` method.

```php
test('app')
    ->expect('App\ValueObjects')
    ->toHaveConstructor();
```

<a name="expect-toHaveDestructor"></a>
### `toHaveDestructor()`

This `toHaveDestructor()` method may be used to ensure that all files within a given namespace have a `__destruct` method.

```php
test('app')
    ->expect('App\ValueObjects')
    ->toHaveDestructor();
```

<a name="expect-toOnlyImplement"></a>
### `toOnlyImplement()`

The `toOnlyImplement()` method may be used to ensure that certain classes are restricted to implementing specific interfaces.

```php
test('app')
    ->expect('App\Responses')
    ->toOnlyImplement('Illuminate\Contracts\Support\Responsable');
```

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

<a name="expect-toUseStrictTypes"></a>
### `toUseStrictTypes()`

The `toUseStrictTypes()` method may be used to ensure that all files within a given namespace utilize strict types.

```php
test('app')
    ->expect('App')
    ->toUseStrictTypes();
```

### Modifiers

Sometimes, you may want to apply the given expectation but excluding certain namespaces or type of files. For that, you may use the following modifiers:

<div class="collection-method-list" markdown="1">

- [`classes()`](#modifier-classes)
- [`enums()`](#modifier-enums)
- [`ignoring()`](#modifier-ignoring)
- [`interfaces()`](#modifier-interfaces)
- [`traits()`](#modifier-traits)

</div>

<a name="modifier-classes"></a>
### `classes()`

The `classes()` modifier allows you to restrict the expectation to only classes.

```php
test('app')
    ->expect('App')
    ->classes()
    ->toBeFinal();
```

<a name="modifier-enums"></a>
### `enums()`

The `enums()` modifier allows you to restrict the expectation to only enums.

```php
test('app')
    ->expect('App\Models')
    ->enums()
    ->toOnlyBeUsedIn('App\Models');
```

<a name="modifier-ignoring"></a>
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

<a name="modifier-interfaces"></a>
### `interfaces()`

The `interfaces()` modifier allows you to restrict the expectation to only interfaces.

```php
test('app')
    ->expect('App')
    ->interfaces()
    ->toExtend('App\Contracts\Contract');
```

<a name="modifier-traits"></a>
### `traits()`

The `traits()` modifier allows you to restrict the expectation to only traits.

```php
test('app')
    ->expect('App')
    ->traits()
    ->toExtend('App\Traits\Trait');
```

---

In this section, you have learned how to perform architectural testing, ensuring that your application or library's architecture meets a specified set of architectural requirements. Moving on, let's explore how to test the coverage of your testing code: [Test Coverage](/docs/test-coverage)
