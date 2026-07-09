---
title: PHPStan
description: Pest's PHPStan plugin teaches PHPStan about Pest, providing accurate type inference for your tests and expectations, and adding Pest-specific rules that catch common mistakes before you run your suite.
---

# PHPStan

**Source code**: [github.com/pestphp/pest-plugin-phpstan](https://github.com/pestphp/pest-plugin-phpstan)

[PHPStan](https://phpstan.org/) is a static analysis tool that finds bugs in your code without running it. By default, however, PHPStan does not understand Pest's functional API — functions like `it()`, `test()`, `expect()`, and the `$this` available inside your test closures.

Pest's PHPStan plugin teaches PHPStan about Pest. It provides accurate type inference for your tests and expectations, and adds a set of Pest-specific rules that catch common mistakes before you run your suite.

To get started, require the plugin via Composer along with PHPStan:

```bash
composer require pestphp/pest-plugin-phpstan --dev
composer require phpstan/phpstan --dev
```

If you use [`phpstan/extension-installer`](https://github.com/phpstan/extension-installer), the plugin is registered automatically. Otherwise, include the extension in your `phpstan.neon` configuration file:

```neon
includes:
    - vendor/pestphp/pest-plugin-phpstan/extension.neon
```

Then, analyse your `tests` directory as usual:

```bash
vendor/bin/phpstan analyse tests
```

---

## Configuration

To get the most accurate results, tell the plugin about the base test case your tests extend. This allows PHPStan to correctly resolve `$this`, along with any custom methods and properties, inside your test closures:

```neon
parameters:
    peststan:
        testCaseClass: Tests\TestCase
```

By default, `testCaseClass` is set to `PHPUnit\Framework\TestCase`.

If your Pest configuration lives outside the default `tests/Pest.php` location, or you have multiple configuration files, you may point the plugin at them so it can resolve globally applied traits and test cases:

```neon
parameters:
    peststan:
        testCaseClass: Tests\TestCase
        pestConfigFiles:
            - tests/Pest.php
            - tests/Feature/Pest.php
```

---

## Type Inference

Once installed, the plugin makes PHPStan aware of Pest's dynamic API. As a result, analysing your test suite becomes as accurate as analysing your application code. Among other things, the plugin understands:

- The type flowing through an `expect()` chain, so matchers such as `toHaveLength()` or `toHaveKey()` know the value they are asserting against.
- The `$this` instance inside test and hook closures, resolving to your configured `testCaseClass` along with its methods and properties.
- Higher order expectations, such as `expect($user)->name->toBe('Nuno')`.
- Properties and methods shared across your tests through `pest()->extend()` and `uses()`.

This means PHPStan can flag genuine mistakes in your tests, like calling a string matcher on an integer, that would otherwise only surface at runtime.

---

## Rules

In addition to type inference, the plugin registers a number of rules that detect mistakes specific to Pest. Each reported error carries a stable identifier (for example, `pest.test.staticClosure`) so you may ignore it precisely when needed.

### Impossible Expectations

Reports expectations that can never pass because the value's type is incompatible with the matcher.

```php
expect(10)->toStartWith('1'); // int can never satisfy toStartWith()
```

### Redundant Expectations

Reports expectations that are always true because the value's type already guarantees the assertion.

```php
expect('pest')->toBeString(); // $value is already known to be a string
```

### Matcher Value Types

Reports matchers called on a value that does not meet their requirements, such as a string, iterable, or countable value.

```php
expect(42)->toHaveLength(2); // toHaveLength() requires a string
```

### Static Test Closures

Reports test and hook closures declared as `static`, which prevents Pest from binding the test case instance to `$this`.

```php
it('does something', static function () { // remove the "static" keyword
    // ...
});
```

### `$this` In `beforeAll()` And `afterAll()`

Reports usage of `$this` inside `beforeAll()` and `afterAll()`, which run in a static context where the test case instance is not available.

### Disallowed Calls In `describe()`

Reports hooks that cannot be used directly inside a `describe()` block, such as `beforeAll()` and `afterAll()`, suggesting the correct alternative.

### Describe Without Tests

Reports `describe()` blocks that contain no tests.

### Duplicate Test Descriptions

Reports two tests sharing the same description within a single file.

### Empty Test Closures

Reports tests with an empty closure body, suggesting you add assertions or chain `->todo()` to mark the test as pending.

### Invalid Repeat Value

Reports `repeat()` calls with a value that is not greater than `0`.

### Invalid Group Names

Reports `group()` calls that are missing a non-empty string argument.

### Redundant Local Uses

Reports `uses()` calls in a test file for a trait or test case that is already applied globally through your Pest configuration.

### Invalid `throws()` Exceptions

Reports `throws()` calls that reference a class that does not exist or is not a `Throwable`.

### Missing `covers()` References

Reports `covers()` calls that reference a class or function that does not exist.

---

Now that you know how to add static analysis to your test suite, you may also be interested in [type coverage](/docs/type-coverage) to ensure your application code is fully typed.
