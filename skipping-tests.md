---
title: Skipping Tests
description: During the development process, there may be times when you need to temporarily disable a test. Rather than commenting out the code, it is recommended to use the `skip` method or the `todo` method, which is specifically designed for this purpose.
---

# Skipping Tests

During the development process, there may be times when you need to temporarily disable a test. Rather than commenting out the code, we recommended using the `skip` method.

```php
it('has home', function () {
    //
})->skip();
```

When running your tests, Pest will inform you about any tests that were skipped.

```php
  ...
  - has home

  Tests: 1 skipped
```

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

You may even invoke the `skip()` method within your `beforeEach()` hook to conveniently skip an entire test file.

```php
beforeEach(function () {
    //
})->skip();
```

## Creating Todos

While skipping tests can be a helpful way to exclude specific tests temporarily from your test suite, it can also lead to situations where skipped tests are forgotten or overlooked. To prevent this, Pest provide a way to create "todos", which are essentially placeholders for tests that need attention.

To begin working with todos, simply invoke the `todo()` method.

```php
it('has home')->todo();
```

If you invoke the `todo()` method on a test, Pest's output will inform you that the test is a todo so you don't forget about it.

```php
  ...
  ↓ it has home

  Tests: 1 todo
```

You can easily view a list of pending todos contained in your test suite by including the `--todos` option when running Pest.

```bash
./vendor/bin/pest --todos
```

---

As your codebase expands, it's advisable to consider enhancing the speed of your test suite. To assist you with that, we offer comprehensive documentation on optimizing your test suite: [Optimizing Tests](/docs/optimizing-tests)
