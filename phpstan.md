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

Beyond type inference, the plugin ships with rules that catch common mistakes before your tests run. For example, it flags impossible assertions — cases where the static type guarantees failure:

```php
expect(42)->toBeString();
// ✘ Calling toBeString() on Expectation<int>; assertion is impossible.
```

It also detects redundant assertions, empty test bodies, static closures (which break `$this` binding), and many other issues. All rules use PHPStan identifiers, so you can suppress them individually:

```neon
parameters:
    ignoreErrors:
        - identifier: pest.test.emptyClosure
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

## Return Types

The plugin provides accurate return types for all of Pest's global functions — `expect()`, `pest()`, `uses()`, `it()`, `describe()`, `beforeEach()`, `afterEach()`, and more. Methods like `not()` and `each()` are typed correctly, and `TestCall` chains (including `with()`, `group()`, `skip()`, `throws()`, etc.) are fully fluent.

---

In this chapter, we've seen how the PHPStan plugin brings static analysis to your Pest test suite. In the next chapter, we'll explore team management: [Team Management](/docs/team-management)
