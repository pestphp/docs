---
title: PHPStan
description: Pest's PHPStan plugin teaches PHPStan about Pest, providing accurate type inference for your tests and expectations, and adding Pest-specific rules that catch common mistakes before you run your suite.
---

# PHPStan

**Source code**: [github.com/pestphp/pest-plugin-phpstan](https://github.com/pestphp/pest-plugin-phpstan)

[PHPStan](https://phpstan.org/) is a static analysis tool that finds bugs in your code without running it. By default, however, PHPStan does not understand Pest's functional API — functions like `it()`, `test()`, `expect()`, and the `$this` available inside your test closures.

Pest's PHPStan plugin teaches PHPStan about Pest. It provides accurate type inference for your tests and expectations, and adds a set of Pest-specific rules that catch common mistakes before you run your suite.

To get started, require the plugin via Composer:

```bash
composer require pestphp/pest-plugin-phpstan --dev
```

As the plugin depends on PHPStan itself, this command will also install PHPStan in your project. If you use [`phpstan/extension-installer`](https://github.com/phpstan/extension-installer), the plugin is registered automatically. Otherwise, include the extension in your `phpstan.neon` configuration file:

```neon
includes:
    - vendor/pestphp/pest-plugin-phpstan/extension.neon
```

Then, analyze your `tests` directory as usual:

```bash
vendor/bin/phpstan analyse tests
```

There is no configuration to maintain. The plugin reads your `Pest.php` files directly — both the `uses(TestCase::class)->in(...)` and `pest()->extend(...)->use(...)->in(...)` styles — and resolves the right test case for each file automatically. The targets given to `in()` behave exactly as they do in Pest: relative or absolute paths, glob patterns, and single files are all supported.

---

## Type Inference

Once installed, the plugin makes PHPStan aware of Pest's dynamic API. As a result, analyzing your test suite becomes as accurate as analyzing your application code.

### The `$this` Instance

Inside test and hook closures, `$this` resolves to the test case bound to that file through your Pest configuration. For example, given the following `Pest.php` file:

```php
pest()->extend(Tests\TestCase::class)
    ->use(Illuminate\Foundation\Testing\RefreshDatabase::class)
    ->in('Feature');
```

Any test within the `Feature` directory will see `$this` typed as `Tests\TestCase`, along with all of its methods and properties:

```php
it('has a home page', function () {
    $this->get('/')->assertOk(); // $this is Tests\TestCase
});
```

Of course, a per-file `uses()` call takes precedence over the directory binding, exactly as it does at runtime.

### Custom Properties

Properties you assign in a `beforeEach()` hook are recognized and typed on `$this` in your tests — whether the hook lives in the test file itself or is attached to your Pest configuration:

```php
beforeEach(function () {
    $this->user = User::factory()->create();
});

it('can update the profile', function () {
    $this->user; // App\Models\User
});
```

If a property is assigned in multiple hooks, its type will be the union of every assigned value. You may also guide the inference with a standard `@var` annotation on the assignment.

### Expectation Chains

The `expect()` function returns a generic `Expectation`, so every matcher knows the exact value it is asserting against. In addition, type-checking matchers narrow the value as the chain progresses:

```php
/** @var int|string $value */
expect($value)   // Expectation<int|string>
    ->toBeInt(); // Expectation<int>
```

The value's type continues to flow through the entire expectation API, including `->and(...)` and `->not`, and matchers like `toBeInstanceOf()` narrow the value to the given class.

### Higher Order Expectations

[Higher order expectations](/docs/higher-order-testing) are fully typed as well. When you access a property or call a method on an expectation, the plugin resolves its type from the underlying value — and after each assertion, the chain returns to the original value, exactly as it does at runtime:

```php
expect($user)
    ->name->toBe('Nuno')
    ->email->toContain('@');
```

Similarly, public methods chained directly onto `it()` or `test()` — higher order tests — resolve against your bound test case, so a call like `it('has users')->actingAsAdmin()` is understood.

---

## Rules

In addition to type inference, the plugin registers a number of rules that detect mistakes specific to Pest. Each reported error carries a stable identifier, so you may ignore any rule precisely via PHPStan's standard `ignoreErrors` configuration:

```neon
parameters:
    ignoreErrors:
        - identifier: pest.expectation.redundant
```

### Impossible Expectations

> Identifier: `pest.expectation.impossible`

Reports type assertions that can never pass because the value's type is incompatible with the matcher:

```php
expect(10)->toBeString(); // an int can never be a string
```

### Redundant Expectations

> Identifier: `pest.expectation.redundant`

Reports type assertions that are always true because the value's type already guarantees them:

```php
expect('pest')->toBeString(); // the value is already known to be a string
```

### Matcher Value Types

> Identifiers: `pest.expectation.requiresString`, `pest.expectation.requiresIterable`, `pest.expectation.requiresCountableOrIterable`

Reports matchers called on a value that does not meet their requirements. For instance, string matchers such as `toStartWith()` require a string, while `toHaveCount()` requires a countable or iterable value:

```php
expect(10)->toStartWith('1'); // toStartWith() requires a string
```

### Static Test Closures

> Identifier: `pest.test.staticClosure`

Reports test and hook closures declared as `static`, which prevents Pest from binding the test case instance to `$this`:

```php
it('does something', static function () { // remove the "static" keyword
    // ...
});
```

### `$this` In `beforeAll()` And `afterAll()`

> Identifiers: `pest.lifecycle.beforeAllThisUsage`, `pest.lifecycle.afterAllThisUsage`

Reports usage of `$this` inside `beforeAll()` and `afterAll()`, which run in a static context where the test case instance is not available, suggesting `beforeEach()` or `afterEach()` instead.

### Disallowed Calls In `describe()`

> Identifiers: `pest.lifecycle.beforeAllDisallowed`, `pest.lifecycle.afterAllDisallowed`

Reports `beforeAll()` and `afterAll()` calls made inside a `describe()` block, where they are not supported, suggesting the correct alternative.

### Describe Without Tests

> Identifier: `pest.describe.withoutTests`

Reports `describe()` blocks that contain no tests. Tests created dynamically — for example, inside a loop or a condition — are counted as you would expect.

### Duplicate Test Descriptions

> Identifier: `pest.test.duplicateDescription`

Reports two tests sharing the same description within a single file. Descriptions given to `it()` are prefixed with "it", exactly as Pest does at runtime, so `it('works')` and `test('it works')` are considered duplicates.

### Empty Test Closures

> Identifier: `pest.test.emptyClosure`

Reports tests with an empty closure body, suggesting you add assertions or chain `->todo()` to mark the test as pending.

### Invalid Repeat Value

> Identifier: `pest.execution.invalidRepeatValue`

Reports `repeat()` calls with a value that is not greater than `0`.

### Invalid Group Names

> Identifier: `pest.group.invalidName`

Reports `group()` calls that are missing a non-empty string argument.

### Redundant Local Uses

> Identifier: `pest.config.redundantLocalUse`

Reports `uses()` and `pest()->use()` calls in a test file for a trait or test case that is already applied to that file globally through your Pest configuration:

```php
// tests/Pest.php
pest()->extend(TestCase::class)->use(RefreshDatabase::class)->in('Feature');

// tests/Feature/ExampleTest.php
uses(RefreshDatabase::class); // already applied through tests/Pest.php
```

### Invalid `throws()` Exceptions

> Identifiers: `pest.throws.classNotFound`, `pest.throws.invalidException`

Reports `throws()` calls that reference a class that does not exist or is not a `Throwable`. Of course, passing a plain string as the expected exception message is perfectly valid, and the plugin will not flag it:

```php
it('rejects invalid input', function () {
    // ...
})->throws(ValidationException::class);

it('fails gracefully', function () {
    // ...
})->throws('Something went wrong'); // treated as the expected message
```

### Missing `covers()` References

> Identifiers: `pest.covers.classNotFound`, `pest.covers.functionNotFound`

Reports `coversClass()`, `coversTrait()`, and `coversFunction()` calls that reference a class, trait, or function that does not exist.

---

In this chapter, we have discussed how Pest's PHPStan plugin brings accurate static analysis to your test suite. In the following chapter, we explore the wider ecosystem of plugins that can enhance your Pest experience: [Plugins →](/docs/plugins)
