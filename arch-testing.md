---
title: Architecture Testing
description: Architecture testing enables you to specify expectations that test whether your application adheres to a set of architectural rules, helping you maintain a clean and sustainable codebase.
---

# Architecture Testing

Architecture testing enables you to specify expectations that test whether your application adheres to a set of architectural rules, helping you maintain a clean and sustainable codebase. The expectations are determined by either relative namespaces, fully qualified namespaces, or function names.

Here is an example of how you can define an architectural rule:

```php
arch()
    ->expect('App')
    ->toUseStrictTypes()
    ->not->toUse(['die', 'dd', 'dump']);

arch()
    ->expect('App\Models')
    ->toBeClasses()
    ->toExtend('Illuminate\Database\Eloquent\Model')
    ->toOnlyBeUsedIn('App\Repositories')
    ->ignoring('App\Models\User');

arch()
    ->expect('App\Http')
    ->toOnlyBeUsedIn('App\Http');

arch()->preset()->php();
arch()->preset()->security()->ignoring('md5');
```

Now, let's dive into the various methods and modifiers available for architectural testing. In this section, you will learn:

- [Expectations](#expectations): Allows to specify granular architectural rules.
- [Presets](#presets): Allows to use predefined sets of granular architectural rules.
- [Modifiers](#modifiers): To exclude or ignore certain types of files, classes, functions or lines of code.

<a name="expectations"></a>
## Expectations

Granular expectations allow you to define specific architectural rules for your application. Here are the available expectations:

<div class="collection-method-list" markdown="1">

- [`toBeAbstract()`](#expect-toBeAbstract)
- [`toBeClasses()`](#expect-toBeClasses)
- [`toBeEnums()`](#expect-toBeEnums)
- [`toBeIntBackedEnums()`](#expect-toBeIntBackedEnums)
- [`toBeInterfaces()`](#expect-toBeInterfaces)
- [`toBeInvokable()`](#expect-toBeInvokable)
- [`toBeFinal()`](#expect-toBeFinal)
- [`toBeReadonly()`](#expect-toBeReadonly)
- [`toBeStringBackedEnums()`](#expect-toBeStringBackedEnums)
- [`toBeTraits()`](#expect-toBeTraits)
- [`toBeUsed()`](#expect-toBeUsed)
- [`toBeUsedIn()`](#expect-toBeUsedIn)
- [`toExtend()`](#expect-toExtend)
- [`toExtendNothing()`](#expect-toExtendNothing)
- [`toImplement()`](#expect-toImplement)
- [`toImplementNothing()`](#expect-toImplementNothing)
- [`toHaveAllMethodsDocumented()`](#expect-toHaveAllMethodsDocumented)
- [`toHaveAllPropertiesDocumented()`](#expect-toHaveAllPropertiesDocumented)
- [`toHaveAttribute()`](#expect-toHaveAttribute)
- [`toHaveFileSystemPermissions()`](#expect-toHaveFileSystemPermissions)
- [`toHaveLineCountLessThan`](#expect-toHaveLineCountLessThan)
- [`toHaveMethod()`](#expect-toHaveMethod)
- [`toHaveMethods()`](#expect-toHaveMethod)
- [`toHavePrivateMethodsBesides()`](#expect-toHavePrivateMethodsBesides)
- [`toHavePrivateMethods()`](#expect-toHavePrivateMethods)
- [`toHaveProtectedMethodsBesides()`](#expect-toHaveProtectedMethodsBesides)
- [`toHaveProtectedMethods()`](#expect-toHaveProtectedMethods)
- [`toHavePublicMethodsBesides()`](#expect-toHavePublicMethodsBesides)
- [`toHavePublicMethods()`](#expect-toHavePublicMethods)
- [`toHavePrefix()`](#expect-toHavePrefix)
- [`toHaveSuffix()`](#expect-toHaveSuffix)
- [`toHaveConstructor()`](#expect-toHaveConstructor)
- [`toHaveDestructor()`](#expect-toHaveDestructor)
- [`toOnlyImplement()`](#expect-toOnlyImplement)
- [`toOnlyUse()`](#expect-toOnlyUse)
- [`toOnlyBeUsedIn()`](#expect-toOnlyBeUsedIn)
- [`toUse()`](#expect-toUse)
- [`toUseTrait()`](#expect-toUseTrait)
- [`toUseTraits()`](#expect-toUseTraits)
- [`toUseNothing()`](#expect-toUseNothing)
- [`toUseStrictTypes()`](#expect-toUseStrictTypes)

</div>

<a name="expect-toBeAbstract"></a>
### `toBeAbstract()`

The `toBeAbstract()` method may be used to ensure that all classes within a given namespace are abstract.

```php
arch('app')
    ->expect('App\Models')
    ->toBeAbstract();
```

<a name="expect-toBeClasses"></a>
### `toBeClasses()`

The `toBeClasses()` method may be used to ensure that all files within a given namespace are classes.

```php
arch('app')
    ->expect('App\Models')
    ->toBeClasses();
```

<a name="expect-toBeEnums"></a>
### `toBeEnums()`

The `toBeEnums()` method may be used to ensure that all files within a given namespace are enums.

```php
arch('app')
    ->expect('App\Enums')
    ->toBeEnums();
```

<a name="expect-toBeIntBackedEnums"></a>
### `toBeIntBackedEnums()`

The `toBeIntBackedEnums()` method may be used to ensure that all enums within a specified namespace are int-backed.

```php
arch('app')
    ->expect('App\Enums')
    ->toBeIntBackedEnums();
```

<a name="expect-toBeInterfaces"></a>
### `toBeInterfaces()`

The `toBeInterfaces()` method may be used to ensure that all files within a given namespace are interfaces.

```php
arch('app')
    ->expect('App\Contracts')
    ->toBeInterfaces();
```

<a name="expect-toBeInvokable"></a>
### `toBeInvokable()`

The `toBeInvokable()` method may be used to ensure that all files within a given namespace are invokable.

```php
arch('app')
    ->expect('App\Actions')
    ->toBeInvokable();
```

<a name="expect-toBeTraits"></a>
### `toBeTraits()`

The `toBeTraits()` method may be used to ensure that all files within a given namespace are traits.

```php
arch('app')
    ->expect('App\Concerns')
    ->toBeTraits();
```

<a name="expect-toBeFinal"></a>
### `toBeFinal()`

The `toBeFinal()` method may be used to ensure that all classes within a given namespace are final.

```php
arch('app')
    ->expect('App\ValueObjects')
    ->toBeFinal();
```

Note that, typically this expectation is used in combination with the `classes()` modifier to ensure that all classes within a given namespace are final.

```php
arch('app')
    ->expect('App')
    ->classes()
    ->toBeFinal();
```

<a name="expect-toBeReadonly"></a>
### `toBeReadonly()`

The `toBeReadonly()` method may be used to ensure that certain classes are immutable and cannot be modified at runtime.

```php
arch('app')
    ->expect('App\ValueObjects')
    ->toBeReadonly();
```

Note that, typically this expectation is used in combination with the `classes()` modifier to ensure that all classes within a given namespace are readonly.

```php
arch('app')
    ->expect('App')
    ->classes()
    ->toBeReadonly();
```

<a name="expect-toBeStringBackedEnums"></a>
### `toBeStringBackedEnums()`

The `toBeStringBackedEnums()` method may be used to ensure that all enums within a specified namespace are string-backed.

```php
arch('app')
    ->expect('App\Enums')
    ->toBeStringBackedEnums();
```

<a name="expect-toBeUsed"></a>
### `toBeUsed()`

The `not` modifier, when combined with the `toBeUsed()` method, enables you to verify that certain classes or functions are not being utilized by your application.

```php
arch('globals')
    ->expect(['dd', 'dump'])
    ->not->toBeUsed();

arch('facades')
    ->expect('Illuminate\Support\Facades')
    ->not->toBeUsed();
```

<a name="expect-toBeUsedIn"></a>
### `toBeUsedIn()`

By combining the `not` modifier with the `toBeUsedIn()` method, you can restrict specific classes and functions from being used within a given namespace.

```php
arch('globals')
    ->expect('request')
    ->not->toBeUsedIn('App\Domain');

arch('globals')
    ->expect('Illuminate\Http')
    ->not->toBeUsedIn('App\Domain');
```

<a name="expect-toExtend"></a>
### `toExtend()`

The `toExtend()` method may be used to ensure that all classes within a given namespace extend a specific class.

```php
arch('app')
    ->expect('App\Models')
    ->toExtend('Illuminate\Database\Eloquent\Model');
```

<a name="expect-toExtendNothing"></a>
### `toExtendNothing()`

The `toExtendNothing()` method may be used to ensure that all classes within a given namespace do not extend any class.

```php
arch('app')
    ->expect('App\ValueObjects')
    ->toExtendNothing();
```

<a name="expect-toImplement"></a>
### `toImplement()`

The `toImplement()` method may be used to ensure that all classes within a given namespace implement a specific interface.

```php
arch('app')
    ->expect('App\Jobs')
    ->toImplement('Illuminate\Contracts\Queue\ShouldQueue');
```

<a name="expect-toImplementNothing"></a>
### `toImplementNothing()`

The `toImplementNothing()` method may be used to ensure that all classes within a given namespace do not implement any interface.

```php
arch('app')
    ->expect('App\ValueObjects')
    ->toImplementNothing();
```

<a name="expect-toHaveAllMethodsDocumented"></a>
### `toHaveAllMethodsDocumented()`

The `toHaveAllMethodsDocumented()` method may be used to ensure that all methods within a given namespace are documented.

```php
arch('app')
    ->expect('App')
    ->toHaveAllMethodsDocumented();
```

<a name="expect-toHaveAllPropertiesDocumented"></a>
### `toHaveAllPropertiesDocumented()`

The `toHaveAllPropertiesDocumented()` method may be used to ensure that all properties within a given namespace are documented.

```php
arch('app')
    ->expect('App')
    ->toHaveAllPropertiesDocumented();
```

<a name="expect-toHaveAttribute"></a>
### `toHaveAttribute()`

The `toHaveAttribute()` method may be used to ensure that a certain class has a specific attribute.

```php
arch('app')
    ->expect('App\Console\Commands')
    ->toHaveAttribute('Symfony\Component\Console\Attribute\AsCommand');
```

<a name="expect-toHaveFileSystemPermissions"></a>
### `toHaveFileSystemPermissions()`

The `toHaveFileSystemPermissions()` method may be used to ensure that all files within a given namespace have specific file system permissions.

```php
arch('app')
    ->expect('App')
    ->not->toHaveFileSystemPermissions('0777');
```

<a name="expect-toHaveLineCountLessThan"></a>
### `toHaveLineCountLessThan()`

The `toHaveLineCountLessThan()` method may be used to ensure that all files within a given namespace have a line count less than a specified value.

```php
arch('app')
    ->expect('App\Models')
    ->toHaveLineCountLessThan(100);
```

<a name="expect-toHaveMethod"></a>
### `toHaveMethod()`

The `toHaveMethod()` method may be used to ensure that a certain class has a specific method.

```php
arch('app')
    ->expect('App\Http\Controllers\HomeController')
    ->toHaveMethod('index');
```

<a name="expect-toHaveMethods"></a>
### `toHaveMethods()`

The `toHaveMethods()` method may be used to ensure that a certain class has specific methods.
```php
arch('app')
    ->expect('App\Http\Controllers\HomeController')
    ->toHaveMethods(['index', 'show']);
```

<a name="expect-toHavePrivateMethodsBesides"></a>
### `toHavePrivateMethodsBesides()`

The `toHavePrivateMethodsBesides()` method may be used to ensure that a certain class does not have any private methods besides the specified ones.

```php
arch('app')
    ->expect('App\Services\PaymentService')
    ->not->toHavePrivateMethodsBesides(['doPayment']);
```

<a name="expect-toHavePrivateMethodsBesides"></a>
### `toHavePrivateMethods()`

The `toHavePrivateMethods()` method may be used to ensure that a certain class does not have any private methods.

```php
arch('app')
    ->expect('App\Services\PaymentService')
    ->not->toHavePrivateMethods();
```

<a name="expect-toHaveProtectedMethodsBesides"></a>
### `toHaveProtectedMethodsBesides()`

The `toHaveProtectedMethodsBesides()` method may be used to ensure that a certain class does not have any protected methods besides the specified ones.

```php
arch('app')
    ->expect('App\Services\PaymentService')
    ->not->toHaveProtectedMethodsBesides(['doPayment']);
```

<a name="expect-toHaveProtectedMethods"></a>
### `toHaveProtectedMethods()`

The `toHaveProtectedMethods()` method may be used to ensure that a certain class does not have any protected methods.

```php
arch('app')
    ->expect('App\Services\PaymentService')
    ->not->toHaveProtectedMethods();
```

<a name="expect-toHavePublicMethodsBesides"></a>
### `toHavePublicMethodsBesides()`

The `toHavePublicMethodsBesides()` method may be used to ensure that a certain class does not have any public methods besides the specified ones.

```php
arch('app')
    ->expect('App\Services\PaymentService')
    ->not->toHavePublicMethodsBesides(['charge', 'refund']);
```

<a name="expect-toHavePublicMethods"></a>
### `toHavePublicMethods()`

The `toHavePublicMethods()` method may be used to ensure that a certain class does not have any public methods.

```php
arch('app')
    ->expect('App\Services\PaymentService')
    ->not->toHavePublicMethods();
```

<a name="expect-toHavePrefix"></a>
### `toHavePrefix()`

The `toHavePrefix()` method may be used to ensure that all files within a given namespace have a specific prefix.

```php
arch('app')
    ->expect('App\Helpers')
    ->not->toHavePrefix('Helper');
```

<a name="expect-toHaveSuffix"></a>
### `toHaveSuffix()`

The `toHaveSuffix()` method may be used to ensure that all files within a given namespace have a specific suffix.

```php
arch('app')
    ->expect('App\Http\Controllers')
    ->toHaveSuffix('Controller');
```

<a name="expect-toHaveConstructor"></a>
### `toHaveConstructor()`

This `toHaveConstructor()` method may be used to ensure that all files within a given namespace have a `__construct` method.

```php
arch('app')
    ->expect('App\ValueObjects')
    ->toHaveConstructor();
```

<a name="expect-toHaveDestructor"></a>
### `toHaveDestructor()`

This `toHaveDestructor()` method may be used to ensure that all files within a given namespace have a `__destruct` method.

```php
arch('app')
    ->expect('App\ValueObjects')
    ->toHaveDestructor();
```

<a name="expect-toOnlyImplement"></a>
### `toOnlyImplement()`

The `toOnlyImplement()` method may be used to ensure that certain classes are restricted to implementing specific interfaces.

```php
arch('app')
    ->expect('App\Responses')
    ->toOnlyImplement('Illuminate\Contracts\Support\Responsable');
```

<a name="expect-toOnlyUse"></a>
### `toOnlyUse()`

The `toOnlyUse()` method may be used to guarantee that certain classes are restricted to utilizing specific functions or classes. For example, you may ensure your models are streamlined and solely dependent on the `Illuminate\Database` namespace, and not, for instance, dispatching queued jobs or events.

```php
arch('models')
    ->expect('App\Models')
    ->toOnlyUse('Illuminate\Database');
```

<a name="expect-toOnlyBeUsedIn"></a>
### `toOnlyBeUsedIn()`

The `toOnlyBeUsedIn()` method enables you to limit the usage of a specific class or set of classes to only particular parts of your application. For instance, you can use this method to confirm that your models are only used by your repositories and not by controllers or service providers.

```php
arch('models')
    ->expect('App\Models')
    ->toOnlyBeUsedIn('App\Repositories');
```

<a name="expect-toUse"></a>
### `toUse()`

By combining the `not` modifier with the `toUse()` method, you can indicate that files within a given namespace should not use specific functions or classes.

```php
arch('globals')
    ->expect('App\Domain')
    ->not->toUse('request');

arch('globals')
    ->expect('App\Domain')
    ->not->toUse('Illuminate\Http');
```

<a name="expect-toUseTrait"></a>
### `toUseTrait()`

The `toUseTrait()` method may be used to ensure that all files within a given namespace use a specific trait.

```php
arch('models')
    ->expect('App\Models')
    ->toUseTrait('Illuminate\Database\Eloquent\SoftDeletes');
```

<a name="expect-toUseTraits"></a>
### `toUseTraits()`

The `toUseTraits()` method may be used to ensure that all files within a given namespace use specific traits.

```php
arch('models')
    ->expect('App\Models')
    ->toUseTraits(['Illuminate\Database\Eloquent\SoftDeletes', 'App\Concerns\CustomTrait']);
```

<a name="expect-toUseNothing"></a>
### `toUseNothing()`

If you want to indicate that particular namespaces or classes should not have any dependencies, you can utilize the `toUseNothing()` method.

```php
arch('value objects')
    ->expect('App\ValueObjects')
    ->toUseNothing();
```

<a name="expect-toUseStrictTypes"></a>
### `toUseStrictTypes()`

The `toUseStrictTypes()` method may be used to ensure that all files within a given namespace utilize strict types.

```php
arch('app')
    ->expect('App')
    ->toUseStrictTypes();
```

<a name="presets"></a>
## Presets

Sometimes, writing arch expectations from scratch can be time-consuming, specifically when working on a new project, and you just want to ensure that the basic architectural rules are met.

Presets are predefined sets of granular expectations that you can use to test your application's architecture.

<div class="collection-method-list" markdown="1">

- [`php`](#preset-php)
- [`security`](#preset-security)
- [`laravel`](#preset-laravel)
- [`strict`](#preset-strict)
- [`strict`](#preset-strict)
- [`custom`](#preset-custom)

</div>

<a name="preset-php"></a>
### `php`

The `php` preset is a predefined set of expectations that can be used on any php project. It's not coupled with any framework or library.

It avoids the usage of `die`, `var_dump`, and similar functions, and ensures you are not using deprecated PHP functions.

```php
arch()->preset()->php();
```

You may find all the expectations included in the `php` preset below in our [source code](https://github.com/pestphp/pest/blob/3.x/src/ArchPresets/Php.php).

<a name="preset-security"></a>
### `security`

The `security` preset is a predefined set of expectations that can be used on any php project. It's not coupled with any framework or library.

It ensures you are not using code that could lead to security vulnerabilities, such as `eval`, `md5`, and similar functions.

```php
arch()->preset()->security();
```

You may find all the expectations included in the `security` preset below in our [source code](https://github.com/pestphp/pest/blob/3.x/src/ArchPresets/Security.php).

<a name="preset-laravel"></a>
### `laravel`

The `laravel` preset is a predefined set of expectations that can be used on [Laravel](https://laravel.com) projects.

It ensures you project's structure is following the well-known Laravel conventions, such as controllers only have `index`, `show`, `create`, `store`, `edit`, `update`, `destroy` as public methods and are always suffixed with `Controller` and so on.

```php
arch()->preset()->laravel();
```

You may find all the expectations included in the `laravel` preset below in our [source code](https://github.com/pestphp/pest/blob/3.x/src/ArchPresets/Laravel.php).

<a name="preset-strict"></a>
### `strict`

The `strict` preset is a predefined set of expectations that can be used on any php project. It's not coupled with any framework or library.

It ensures you are using strict types in all your files, that all your classes are final, and more.

```php
arch()->preset()->strict();
```

You may find all the expectations included in the `strict` preset below in our [source code](https://github.com/pestphp/pest/blob/3.x/src/ArchPresets/Strict.php).

<a name="preset-relaxed"></a>
### `relaxed`

The `relaxed` preset is a predefined set of expectations that can be used on any php project. It's not coupled with any framework or library.

It is the opposite of the `strict` preset, ensuring you are not using strict types in all your files, that all your classes are not final, and more.

```php
arch()->preset()->relaxed();
```

You may find all the expectations included in the `relaxed` preset below in our [source code](https://github.com/pestphp/pest/blob/3.x/src/ArchPresets/Relaxed.php).

<a name="preset-custom"></a>
### `custom`

Typically you don't need to use the `custom` preset, as you can use the `arch()` method to write your granular expectations. However, if you want to create your own preset, you can use the `custom` preset.

This may be useful if you have a set of expectations that you use frequently across multiple projects, or if you are plugin author and want to provide a set of expectations for your users.
```php
pest()->preset('ddd', function () {
    return [
        expect('Infrastructure')->toOnlyBeUsedIn('Application'),
        expect('Domain')->toOnlyBeUsedIn('Application'),
    ];
});
```

With the `preset` method, you may have access to the application PSR-4 namespaces on the first argument of your closure's callback.

```php
pest()->preset('silex', function (array $userNamespaces) {
    dump($userNamespaces); // ['App\\']
});
```

<a name="modifiers"></a>
## Modifiers

Sometimes, you may want to apply the given expectation but excluding certain types of files, or ignoring certain classes, functions, or specific lines of code. For that, you may use the following methods:

<div class="collection-method-list" markdown="1">

- [`ignoring()`](#modifier-ignoring)
- [`classes()`](#modifier-classes)
- [`enums()`](#modifier-enums)
- [`interfaces()`](#modifier-interfaces)
- [`traits()`](#modifier-traits)

</div>

<a name="modifier-ignoring"></a>
### `ignoring()`

When defining your architecture rules, you can use the `ignoring()` method to exclude certain namespaces or classes that would otherwise be included in the rule definition.

```php
arch()
    ->preset()
    ->php()
    ->ignoring('die');

arch()
    ->expect('Illuminate\Support\Facades')
    ->not->toBeUsed()
    ->ignoring('App\Providers');
```

You may also ignore a specific line of code by using the `@pest-arch-ignore-line` or `@pest-arch-ignore-next-line` directives.

```php
die('This line will be ignored'); // @pest-arch-ignore-line

// @pest-arch-ignore-next-line
die('This line will be ignored');
```

In some cases, certain components may not be regarded as "dependencies" as they are part of the native PHP library. To customize the definition of "native" code and exclude it during testing, Pest allows you to specify what to ignore.

For example, if you do not want to consider Laravel a "dependency", you can use the `arch()` method inside the `beforeEach()` function to disregard any code within the "Illuminate" namespace. This approach allows you to focus only on the actual dependencies of your application.

```php
// tests/Pest.php
pest()->beforeEach(function () {
    $this->arch()->ignore([
        'Illuminate',
    ])->ignoreGlobalFunctions();
});
```

<a name="modifier-classes"></a>
### `classes()`

The `classes()` modifier allows you to restrict the expectation to only classes.

```php
arch('app')
    ->expect('App')
    ->classes()
    ->toBeFinal();
```

<a name="modifier-enums"></a>
### `enums()`

The `enums()` modifier allows you to restrict the expectation to only enums.

```php
arch('app')
    ->expect('App\Models')
    ->enums()
    ->toOnlyBeUsedIn('App\Models');
```

<a name="modifier-interfaces"></a>
### `interfaces()`

The `interfaces()` modifier allows you to restrict the expectation to only interfaces.

```php
arch('app')
    ->expect('App')
    ->interfaces()
    ->toExtend('App\Contracts\Contract');
```

<a name="modifier-traits"></a>
### `traits()`

The `traits()` modifier allows you to restrict the expectation to only traits.

```php
arch('app')
    ->expect('App')
    ->traits()
    ->toExtend('App\Traits\Trait');
```

---

In this section, you have learned how to perform architectural testing, ensuring that your application or library's architecture meets a specified set of architectural requirements. Next, have you ever wondered how to test the performance of your code? Let's explore [Stress Testing](/docs/stress-testing).
