---
title: Skipping Tests
description: Sometimes you may need to temporarily disable a test. Rather than commenting out the code, Pest lets you skip it gracefully with the `skip()` method.
---

# Skipping Tests

During development, there may be times when you need to temporarily disable a test. Rather than commenting out the code, you may use the `skip()` method:

```php
it('has home', function () {
    //
})->skip();
```

When running your tests, Pest will inform you about any tests that were skipped.

<div class="terminal not-prose"><div class="l">&nbsp;&nbsp;<span class="chip warn">WARN</span>&nbsp;&nbsp;<span class="white">Tests\Feature\HomepageTest</span></div><div class="l">&nbsp;&nbsp;<span class="amber">-</span> it has home</div></div>

You may also provide a reason for skipping the test, which Pest will display when running your suite:

```php
it('has home', function () {
    //
})->skip('temporarily unavailable');
```

Sometimes you may wish to skip a test based on a given condition. In these cases, you may provide a boolean value as the first argument to the `skip()` method. The test will only be skipped if that value evaluates to `true`:

```php
it('has home', function () {
    //
})->skip($condition == true, 'temporarily unavailable');
```

Alternatively, you may pass a closure as the first argument to the `skip()` method to defer evaluation of the condition until the `beforeEach()` hook of your test case has run:

```php
it('has home', function () {
    //
})->skip(fn () => DB::getDriverName() !== 'mysql', 'db driver not supported');
```

You may also skip tests based on the environment in which they are running using the `skipLocally()` or `skipOnCi()` methods:

```php
it('has home', function () {
    //
})->skipLocally(); // or skipOnCi()
```

To skip a test on a particular operating system, you may make use of the `skipOnWindows()`, `skipOnMac()`, or `skipOnLinux()` methods:

```php
it('has home', function () {
    //
})->skipOnWindows(); // or skipOnMac() or skipOnLinux() ...
```

Alternatively, you may skip a test on all operating systems except one by using `onlyOnWindows()`, `onlyOnMac()`, or `onlyOnLinux()`:

```php
it('has home', function() {
    //
})->onlyOnWindows(); // or onlyOnMac() or onlyOnLinux() ...
```

Sometimes you may wish to skip a test on a specific PHP version. In these cases, you may use the `skipOnPhp()` method:

```php
it('has home', function () {
    //
})->skipOnPhp('>=8.0.0');
```

The valid operators for the `skipOnPhp()` method are `>`, `>=`, `<`, and `<=`.

Finally, you may even invoke the `skip()` method within your `beforeEach()` hook to conveniently skip an entire test file:

```php
beforeEach()->skip(); // or skipOnCi(), etc...
```

## Creating Todos

Sometimes you may wish to add a few empty tests so that you do not forget to write them later. The `todo()` method is helpful in this situation:

```php
it('has home', function () {
    //
})->todo();
```

---

As your codebase expands, you may wish to improve the speed of your test suite. To help you with that, we offer detailed documentation on optimizing your suite: [Optimizing Tests](/docs/optimizing-tests)
