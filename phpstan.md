---
title: PHPStan
description: Pest's PHPStan extension provides type-safe expectations, proper $this binding in test closures, and accurate return types for all Pest functions.
---

# PHPStan

**Source code**: [github.com/pestphp/pest-plugin-phpstan](https://github.com/pestphp/pest-plugin-phpstan)

While Pest is known for its expressive syntax, it can be challenging for static analysis tools like PHPStan to understand the types flowing through expectations, test closures, and lifecycle hooks. The PHPStan plugin bridges this gap, giving you precise type inference across all of Pest's features.

```bash
composer require pestphp/pest-plugin-phpstan --dev
```

If you use [phpstan/extension-installer](https://github.com/phpstan/extension-installer), the extension registers itself automatically. Otherwise, add it to your `phpstan.neon`:

```neon
includes:
    - vendor/pestphp/pest-plugin-phpstan/extension.neon
```

## Type-Safe Expectations

The plugin provides generic type inference for the `expect()` function, so PHPStan knows the exact type of the value being asserted:

```php
expect('hello');   // Expectation<string>
expect(42);        // Expectation<int>
expect($user);     // Expectation<User>
```

This extends to type-narrowing assertions — after calling `toBeString()`, PHPStan refines the generic parameter:

```php
/** @var int|string $value */
expect($value)->toBeString();
// PHPStan now knows the expectation wraps a string
```

The same applies to chaining with `and()`:

```php
expect('hello')
    ->toBeString()
    ->and(42)       // Expectation<int>
    ->toBeInt();
```

## `$this` in Test Closures

Inside every test and lifecycle hook, Pest binds `$this` to the TestCase instance. The plugin reads your `Pest.php` file to discover the correct TestCase class, so PHPStan knows exactly what `$this` is:

```php
// tests/Pest.php
uses(Tests\TestCase::class)->in('Feature');

// tests/Feature/ExampleTest.php
it('can access test case methods', function () {
    $this->get('/');  // PHPStan knows $this is Tests\TestCase
});

beforeEach(function () {
    $this->assertTrue(true);
});
```

The plugin also infers property types set inside `beforeEach` hooks, so you get accurate types without `@var` annotations:

```php
beforeEach(function () {
    $this->post  = new Post;
    $this->title = 'Hello';
});

it('knows the property types', function () {
    $this->post->title; // PHPStan knows $this->post is Post
    strlen($this->title); // fine — it's a string
});
```

## Static Analysis Rules

Beyond type inference, the plugin ships with rules that catch common mistakes before your tests run. All rules use PHPStan identifiers, so you can suppress them individually:

```neon
parameters:
    ignoreErrors:
        - identifier: pest.test.emptyClosure
```

### Empty Test Body

Detects tests whose closure contains no statements.

- identifier: `pest.test.emptyClosure`

```php
it('does something'); // fine — todo test
it('does something', function () {});
// ✘ Test 'does something' has an empty closure body. Did you forget to add assertions?
```

### Static Test Closure

Pest binds `$this` inside every test closure to the `TestCase` instance. Marking the closure `static` prevents that binding.

- identifier: `pest.test.staticClosure`

```php
it('example', static function () {
// ✘ Test closure passed to it() must not be static.
    expect(true)->toBeTrue();
});
```

### Lifecycle Hooks Inside `describe()`

Pest does not support `beforeAll()` or `afterAll()` inside `describe()` blocks — calling them throws at runtime.

- identifiers: `pest.lifecycle.beforeAllDisallowed`, `pest.lifecycle.afterAllDisallowed`

```php
describe('suite', function () {
    beforeAll(function () { /* ... */ });
    // ✘ beforeAll() cannot be used inside describe() blocks.

    afterAll(function () { /* ... */ });
    // ✘ afterAll() cannot be used inside describe() blocks.
});
```

### Invalid `repeat()` Count

`repeat()` requires a positive integer greater than zero.

- identifier: `pest.execution.invalidRepeatValue`

```php
it('runs multiple times', function () { /* ... */ })->repeat(0);
// ✘ repeat() requires a value greater than 0, got 0.
```

### Duplicate Test Description

Two tests in the same file with the same description will collide at runtime.

- identifier: `pest.test.duplicateDescription`

```php
it('does something', fn () => expect(1)->toBe(1));
it('does something', fn () => expect(2)->toBe(2));
// ✘ A test with the description 'it does something' already exists in this file.
```

### Impossible Assertions

When the static type already makes an assertion impossible, the plugin reports it.

- identifier: `pest.expectation.impossible`

```php
expect(42)->toBeString();
// ✘ Calling toBeString() on Expectation<int>; assertion is impossible.

expect('hello')->toBeNull();
// ✘ Calling toBeNull() on Expectation<string>; assertion is impossible.
```

Covered assertions: `toBeString`, `toBeInt`, `toBeFloat`, `toBeBool`, `toBeTrue`, `toBeFalse`, `toBeNull`, `toBeArray`, `toBeList`, `toBeObject`, `toBeCallable`, `toBeIterable`, `toBeNumeric`, `toBeScalar`, `toBeInstanceOf`.

### Redundant Assertions

When the static type already guarantees an assertion will always succeed, the assertion is redundant.

- identifier: `pest.expectation.redundant`

```php
expect(true)->toBeTrue();
// ✘ Calling toBeTrue() on Expectation<true>; assertion is redundant.

expect('hello')->toBeString();
// ✘ Calling toBeString() on Expectation<string>; assertion is redundant.

expect(42)->toBeNumeric();
// ✘ Calling toBeNumeric() on Expectation<int>; assertion is redundant.
```

Covered assertions: `toBeString`, `toBeInt`, `toBeFloat`, `toBeBool`, `toBeTrue`, `toBeFalse`, `toBeNull`, `toBeArray`, `toBeList`, `toBeObject`, `toBeCallable`, `toBeIterable`, `toBeNumeric`, `toBeScalar`, `toBeInstanceOf`.

### Incompatible Value Type

Some expectation methods require the value to satisfy a pre-condition.

- identifiers: `pest.expectation.requiresIterable`, `pest.expectation.requiresString`

```php
expect(42)->each(fn ($e) => $e->toBeInt());
// ✘ Calling each() on Expectation<int>; matcher requires iterable.

expect(42)->toBeJson();
// ✘ Calling toBeJson() on Expectation<int>; matcher requires string.
```

Methods requiring an iterable: `each`, `sequence`. Methods requiring a string: `json`, `toStartWith`, `toEndWith`, `toBeJson`, `toBeDirectory`, `toBeFile`, `toBeReadableFile`, `toBeWritableFile`, `toBeReadableDirectory`, `toBeWritableDirectory`.

### `$this` in `beforeAll()`

`beforeAll()` runs once in a static context before any tests in the file. `$this` is not available.

- identifier: `pest.lifecycle.beforeAllThisUsage`

```php
beforeAll(function () {
    $this->db = new Database; // ✘ beforeAll() runs in static context — $this is not available. Use beforeEach() instead.
});
```

### Invalid `throws()` Argument

`throws()` accepts a class name that implements `Throwable`.

- identifiers: `pest.throws.classNotFound`, `pest.throws.invalidException`

```php
it('fails', function () { ... })->throws('App\NonExistentException');
// ✘ Class App\NonExistentException passed to throws() does not exist.

it('fails', function () { ... })->throws(stdClass::class);
// ✘ throws() expects a Throwable class, got stdClass.
```

### Non-Existent Symbol in `covers()`

`coversClass()`, `coversTrait()`, and `coversFunction()` reference symbols by name. The plugin verifies those symbols exist.

- identifiers: `pest.covers.classNotFound`, `pest.covers.functionNotFound`

```php
it('covers something', function () { ... })->coversClass('App\Nonexistent\Service');
// ✘ Class App\Nonexistent\Service referenced in coversClass() does not exist.
```

### Empty `describe()` Block

A `describe()` block that contains no `it()` or `test()` calls (only hooks, or nothing at all) is likely a mistake.

- identifier: `pest.describe.withoutTests`

```php
describe('UserService', function () {
    beforeEach(fn () => null);
    // ✘ describe() block 'UserService' contains no tests.
});
```

### Invalid `group()` Name

`group()` requires at least one non-empty, non-whitespace string argument.

- identifier: `pest.group.invalidName`

```php
it('example', fn () => null)->group('');
// ✘ group() requires a non-empty string argument.
```

## Configuration

The plugin works out of the box — no configuration needed. It discovers your `Pest.php` files automatically from PHPStan's analysis paths.

If you need a global default TestCase class, set it in `phpstan.neon`:

```neon
parameters:
    pest:
        testCaseClass: App\Testing\TestCase
```

And if your `Pest.php` files are outside PHPStan's analysis paths, specify them explicitly:

```neon
parameters:
    pest:
        pestConfigFiles:
            - tests/Pest.php
```

## Migrating from PestStan

If you were using the original `mrpunyapal/peststan` package, here's how to migrate to the new official Pest PHPStan plugin.

**1. Update your Composer dependencies**

```bash
composer remove mrpunyapal/peststan --dev
composer require pestphp/pest-plugin-phpstan --dev
```

**2. Update your `phpstan.neon` configuration**

```neon
# Old
includes:
    - vendor/mrpunyapal/peststan/extension.neon

# New
includes:
    - vendor/pestphp/pest-plugin-phpstan/extension.neon
```

**3. Update configuration parameters**

```neon
# Old
parameters:
    peststan:
        testCaseClass: App\Testing\TestCase

# New
parameters:
    pest:
        testCaseClass: App\Testing\TestCase
```

**4. Update namespace references in your code**

```php
// Old
use PestStan\SomeClass;

// New
use Pest\Phpstan\SomeClass;
```

> The new plugin requires PHP ^8.4 and Pest ^3.0, ^4.0, or ^5.0.

---

In this chapter, we've seen how the PHPStan plugin brings static analysis to your Pest test suite. In the next chapter, we'll explore automated refactoring with Rector: [Rector](/docs/rector)
