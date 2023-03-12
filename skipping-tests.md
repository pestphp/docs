---
title: Skipping Tests
description: During the development process, there may be times when you need to temporarily disable a test. Rather than commenting out the code, it is recommended to use the `skip` method or the `todo` method, which is specifically designed for this purpose.
---

# Skipping Tests

During the development process, there may be times when you need to temporarily disable a test. Rather than commenting out the code, it is recommended to use the `skip` method, which is specifically designed for this purpose.

```php
it('has home', function () {
    //
})->skip();
```

Naturally, when you use the `skip` method instead of running the test, the output will provide you with feedback indicating that the test was skipped.

```php
  ...
  - has home

  Tests: 1 skipped
```

It is worth noting that you have the option to provide a reason for skipping the test.

```php
it('has home', function () {
    //
})->skip('temporarily unavailable');
```

In addition, there may be cases where you want to skip a test based on a certain condition.

```php
it('has home', function () {
    //
})->skip($condition == true, 'temporarily unavailable');
```

Similar to datasets, if the condition for skipping a test relies on the underlying test case, you can use a closure.

```php
it('has home', function () {
    //
})->skip(fn() => DB::getDriverName() !== 'mysql', 'db driver not supported');
```

You can use the skip method within your beforeEach hook to conveniently skip an entire file when needed.

```php
beforeEach(function () {
    //
})->skip();
```

## Creating Todos

While skipping tests can be a helpful way to exclude specific tests temporarily from your test suite, it can also lead to situations where skipped tests are forgotten or overlooked. To prevent this, Pest provide a way to create todos, which are essentially placeholders for tests that need attention.

To begin working with "todos" in Pest, you can easily mark a test as a todo by using the `todo()` method within the test itself.

```php
it('has home')->todo();
```

If you utilize the todo method in place of executing the test, the resulting output will inform you that the test is a todo, offering feedback as a natural consequence.

```php
  ...
  ↓ it has home

  Tests: 1 todo
```

At any point in time, you have the ability to view a list of todos in your test suite by utilizing the `--todos` option.

```bash
./vendor/bin/pest --todos
```

---

As your codebase expands, it's advisable to consider enhancing the speed of your test suite. To assist you with that, we offer a comprehensive documentation: [Optimizating Tests](/docs/optimizing-tests)
