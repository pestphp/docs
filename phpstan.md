---
title: PHPStan
description: Pest's PHPStan extension provides type-safe expectations, proper $this binding in test closures, and accurate return types for all Pest functions.
---

# PHPStan

**Source code**: [github.com/pestphp/pest-plugin-phpstan](https://github.com/pestphp/pest-plugin-phpstan)

Pest's PHPStan extension brings static analysis to your test suite. It provides type-safe expectations, proper `$this` binding in test closures, and accurate return types for all Pest functions.

## Requirements

- PHP ^8.4
- PHPStan ^2.0
- Pest PHP ^3.0, ^4.0, or ^5.0

## Installation

To install the plugin, require it via Composer:

```bash
composer require pestphp/pest-plugin-phpstan --dev
```

If you have [phpstan/extension-installer](https://github.com/phpstan/extension-installer) (recommended), the extension is registered automatically.

Otherwise, add it manually to your `phpstan.neon` or `phpstan.neon.dist`:

```neon
includes:
    - vendor/pestphp/pest-plugin-phpstan/extension.neon
```

## Features

### Generic `expect()` Function

The extension provides generic type inference for Pest's `expect()` function, so PHPStan knows the exact type of the expectation value:

```php
expect('hello');           // Expectation<string>
expect(42);                // Expectation<int>
expect(['a' => 1]);        // Expectation<array{a: int}>
expect($user);             // Expectation<User>
expect();                  // Expectation<null>
```

### Type Narrowing Assertions

Type-checking assertion methods narrow the generic type parameter, so PHPStan tracks the type through assertion chains:

```php
/** @var int|string $value */
$value = getValue();

expect($value)->toBeString();
// PHPStan now knows the expectation wraps a string

expect($value)->toBeInstanceOf(User::class);
// PHPStan now knows the expectation wraps a User
```

Supported type-narrowing assertions: `toBeString`, `toBeInt`, `toBeFloat`, `toBeBool`, `toBeArray`, `toBeList`, `toBeObject`, `toBeCallable`, `toBeIterable`, `toBeNumeric`, `toBeScalar`, `toBeResource`, `toBeTrue`, `toBeFalse`, `toBeNull`, `toBeInstanceOf`.

### Type-Safe `and()` Chaining

The `and()` method properly changes the generic type parameter, enabling type-safe assertion chains:

```php
expect('hello')
    ->toBeString()       // Expectation<string>
    ->and(42)            // Expectation<int>
    ->toBeInt()          // Expectation<int>
    ->and(['a', 'b'])    // Expectation<array{string, string}>
    ->toHaveCount(2);    // Expectation<array{string, string}>
```

### `$this` Binding in Test Closures

The extension ensures `$this` is properly typed inside all Pest test closures and lifecycle hooks. It auto-detects your TestCase class from your `Pest.php` configuration file:

```php
// tests/Pest.php
uses(Tests\TestCase::class)->in('Feature');

// tests/Feature/ExampleTest.php
it('can access test case methods', function () {
    $this->get('/');  // PHPStan knows $this is Tests\TestCase
});

beforeEach(function () {
    $this->assertTrue(true);   // Works in hooks too
});
```

Supported functions: `it()`, `test()`, `describe()`, `beforeEach()`, `afterEach()`, `beforeAll()`, `afterAll()`.

### Dynamic Properties in Test Closures

Pest allows setting properties on `$this` inside `beforeEach` hooks. The extension reads those assignments and infers the exact type:

```php
beforeEach(function () {
    $this->post   = new Post;                    // Post
    $this->title  = 'Hello';                     // 'Hello' (constant string)
    $this->count  = 42;                          // 42 (constant int)
    $this->active = true;                        // true
});

it('knows the property types', function () {
    $this->post->title;          // PHPStan knows $this->post is Post
    strlen($this->title);        // fine — PHPStan knows it is a string
});
```

For method-call chains such as factory calls, annotate the local variable with `@var` to guide inference:

```php
beforeEach(function () {
    /** @var User $user */
    $user        = User::factory()->create();
    $this->user  = $user;        // User
});
```

If the same property is set by multiple hooks the type is unioned:

```php
beforeEach(function () { $this->item = new Post; });
beforeEach(function () { $this->item = new Comment; });

it('sees the union', function () {
    $this->item;  // Post|Comment
});
```

Properties that are never set in a hook remain `mixed`.

## Configuration

### Automatic TestCase Detection

The plugin reads your `Pest.php` files to determine which TestCase class is used in each test directory. It supports the `uses()` pattern:

```php
// uses(TestCase::class)->in('Feature');
```

No configuration needed — it discovers `Pest.php` files automatically from your PHPStan `paths`.

### Manual TestCase Override

If auto-detection doesn't work for your setup, or you want a global default, set it in your `phpstan.neon`:

```neon
parameters:
    pest:
        testCaseClass: App\Testing\TestCase
```

### Explicit Pest.php Paths

If your `Pest.php` files aren't within PHPStan's analysis paths, you can specify them explicitly:

```neon
parameters:
    pest:
        pestConfigFiles:
            - tests/Pest.php
```

## Static Analysis Rules

The plugin ships with rules that catch common Pest mistakes at static analysis time, before your tests run.

### Empty Test Body

Detects tests whose closure contains no statements:

```php
it('does something'); // fine — todo test
it('does something', function () {});
// ✘ Test 'does something' has an empty closure body. Did you forget to add assertions?
```

Identifier: `pest.test.emptyClosure`

### Static Test Closure

Pest binds `$this` inside every test closure to the `TestCase` instance. Marking the closure `static` prevents that binding:

```php
it('example', static function () {
// ✘ Test closure passed to it() must not be static.
    expect(true)->toBeTrue();
});
```

Identifier: `pest.test.staticClosure`

### Lifecycle Hooks Inside `describe()`

Pest does not support `beforeAll()` or `afterAll()` inside `describe()` blocks:

```php
describe('suite', function () {
    beforeAll(function () { /* ... */ });
    // ✘ beforeAll() cannot be used inside describe() blocks.

    afterAll(function () { /* ... */ });
    // ✘ afterAll() cannot be used inside describe() blocks.
});
```

Identifiers: `pest.lifecycle.beforeAllDisallowed`, `pest.lifecycle.afterAllDisallowed`

### Invalid `repeat()` Count

`repeat()` requires a positive integer greater than zero:

```php
it('runs multiple times', function () { /* ... */ })->repeat(0);
// ✘ repeat() requires a value greater than 0, got 0.
```

Identifier: `pest.execution.invalidRepeatValue`

### Duplicate Test Description

Two tests in the same file with the same description will collide at runtime:

```php
it('does something', fn () => expect(1)->toBe(1));
it('does something', fn () => expect(2)->toBe(2));
// ✘ A test with the description 'it does something' already exists in this file.
```

Identifier: `pest.test.duplicateDescription`

### Impossible Assertions

When the static type already makes an assertion impossible, the plugin reports it:

```php
expect(42)->toBeString();
// ✘ Calling toBeString() on Expectation<int>; assertion is impossible.

expect('hello')->toBeNull();
// ✘ Calling toBeNull() on Expectation<string>; assertion is impossible.
```

Identifier: `pest.expectation.impossible`

### Redundant Assertions

When the static type already guarantees an assertion will always succeed, the assertion is redundant:

```php
expect(true)->toBeTrue();
// ✘ Calling toBeTrue() on Expectation<true>; assertion is redundant.

expect('hello')->toBeString();
// ✘ Calling toBeString() on Expectation<string>; assertion is redundant.
```

Identifier: `pest.expectation.redundant`

### Incompatible Value Type

Some expectation methods require the value to satisfy a pre-condition:

```php
expect(42)->each(fn ($e) => $e->toBeInt());
// ✘ Calling each() on Expectation<int>; matcher requires iterable.

expect(42)->toBeJson();
// ✘ Calling toBeJson() on Expectation<int>; matcher requires string.
```

Identifiers: `pest.expectation.requiresIterable`, `pest.expectation.requiresString`

### `$this` in `beforeAll()`

`beforeAll()` runs once in a static context before any tests in the file:

```php
beforeAll(function () {
    $this->db = new Database; // ✘ beforeAll() runs in static context — $this is not available.
});
```

Identifier: `pest.lifecycle.beforeAllThisUsage`

### Invalid `throws()` Argument

`throws()` accepts a class name that implements `Throwable`:

```php
it('fails', function () { ... })->throws('App\NonExistentException');
// ✘ Class App\NonExistentException passed to throws() does not exist.
```

Identifiers: `pest.throws.classNotFound`, `pest.throws.invalidException`

### Non-Existent Symbol in `covers()`

`coversClass()`, `coversTrait()`, and `coversFunction()` reference symbols by name. The plugin verifies those symbols exist:

```php
it('covers something', function () { ... })->coversClass('App\Nonexistent\Service');
// ✘ Class App\Nonexistent\Service referenced in coversClass() does not exist.
```

Identifiers: `pest.covers.classNotFound`, `pest.covers.functionNotFound`

### Empty `describe()` Block

A `describe()` block that contains no `it()` or `test()` calls is likely a mistake:

```php
describe('UserService', function () {
    beforeEach(fn () => null);
    // ✘ describe() block 'UserService' contains no tests.
});
```

Identifier: `pest.describe.withoutTests`

### Invalid `group()` Name

`group()` requires at least one non-empty, non-whitespace string argument:

```php
it('example', fn () => null)->group('');
// ✘ group() requires a non-empty string argument.
```

Identifier: `pest.group.invalidName`

## Ignoring Rules

All rules use PHPStan identifiers, so you can suppress them selectively in your baseline or inline:

```neon
# phpstan.neon
parameters:
    ignoreErrors:
        - identifier: pest.test.emptyClosure
```

```php
/** @phpstan-ignore pest.test.staticClosure */
it('example', static fn () => expect(true)->toBeTrue());
```

## Return Types

Accurate return types for Pest's core global functions:

| Function | Return Type |
|----------|-------------|
| `expect($value)` | `Expectation<TValue>` |
| `pest()` | `Configuration` |
| `uses(...)` | `UsesCall` |
| `it()` / `test()` / `todo()` | `TestCall` |
| `describe()` | `DescribeCall` |
| `beforeEach()` | `BeforeEachCall` |
| `afterEach()` | `AfterEachCall` |
| `fixture()` | `string` |
| `beforeAll()` / `afterAll()` | `null` |
| `dataset()` / `covers()` / `mutates()` | `null` |

### `not()` and `each()` Return Types

```php
expect('hello')->not();    // OppositeExpectation<string>
expect([1, 2])->each();    // EachExpectation<array{int, int}>
```

### TestCall Chaining

All `TestCall` methods are properly typed for fluent chaining:

```php
it('does something', function () { /* ... */ })
    ->with(['a', 'b'])
    ->group('unit', 'feature')
    ->skip(false)
    ->depends('other test')
    ->throws(RuntimeException::class)
    ->repeat(3);
```

When you set `pest.testCaseClass` to a custom class, the plugin also exposes that class's public helper methods on `TestCall` chains:

```php
it('uses a custom helper')->publicHelper();
```
