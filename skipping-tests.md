---
title: Skipping Tests
description: During the development process, there may be times when you need to temporarily disable a test. Rather than commenting out the code, we recommended using the `skip()` method.
---

# Skipping Tests

During the development process, there may be times when you need to temporarily disable a test. Rather than commenting out the code, we recommended using the `skip()` method.

```php
it('has home', function () {
    //
})->skip();
```

When running your tests, Pest will inform you about any tests that were skipped.

<div class="code-snippet">
    <img src="/assets/img/skip.webp?1" style="--lines: 2" />
</div>

You may also provide the reason for skipping the test, which Pest will display when running your tests.

```php
it('has home', function () {
    //
})->skip('temporarily unavailable');
```

In addition, there may be times when you want to skip a test based on a given condition. In these cases, you may provide a boolean value as the first argument to the `skip()` method. This test will only be skipped if the boolean value evaluates to `true`.

```php
it('has home', function () {
    //
})->skip($condition == true, 'temporarily unavailable');
```

You may pass a closure as the first argument to the `skip()` method to defer the evaluation of the condition until the `beforeEach()` hook of your test case has been executed.

```php
it('has home', function () {
    //
})->skip(fn () => DB::getDriverName() !== 'mysql', 'db driver not supported');
```

To skip a test on a particular operating system, you can make use of the `skipOnWindows()`, `skipOnMac()`, or `skipOnLinux()`.

```php
it('has home', function () {
    //
})->skipOnWindows(); // or skipOnMac() or skipOnLinux() ...
```

Alternatively, you can skip a test on all operating systems except one by using `onlyOnWindows()`, `onlyOnMac()`, or `onlyOnLinux()`.

```php
it('has home', function() {
    //
})->onlyOnWindows(); // or onlyOnMac() or onlyOnLinux() ...
```

Sometimes, you may want to skip a test on a specific PHP version. In these cases, you may use the `skipOnPhp()` method.

```php
it('has home', function () {
    //
})->skipOnPhp('>=8.0.0');
```

The valid operators for the `skipOnPhp()` method are `>`, `>=`, `<`, and `<=`.

Finally, you may even invoke the `skip()` method within your `beforeEach()` hook to conveniently skip an entire test file.

```php
beforeEach(function () {
    //
})->skip();
```

---

As your codebase expands, it's advisable to consider enhancing the speed of your test suite. To assist you with that, we offer comprehensive documentation on optimizing your test suite: [Optimizing Tests](/docs/optimizing-tests)
