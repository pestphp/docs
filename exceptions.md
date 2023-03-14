---
title: Exceptions
description: In PHP, when testing behavior, it may be necessary to verify whether an exception or error has been thrown.
---

# Exceptions

In PHP, when testing behavior, it may be necessary to verify whether an exception or error has been thrown.

To write a test that expects an exception to be thrown, you can use the following code.

```php
it('throws exception', function () {
    throw new Exception('Something happened.');
})->throws(Exception::class);
```

If you also want to assert the exception message, you need to provide a second argument to the throws method.

```php
it('throws exception', function () {
    throw new Exception('Something happened.');
})->throws(Exception::class, 'Something happened.');
```

If the exception type is not relevant and you're only concerned with the message, you can directly pass the message without specifying the exception type.

```php
it('throws exception', function () {
    throw new Exception('Something happened.');
})->throws('Something happened.');
```

You can also use the `throwsIf` method to conditionally verify an exception if a specific Boolean expression is true.
```php
it('throws exception', function () {
    //
})->throwsIf(fn() => DB::getDriverName() === 'mysql', Exception::class, 'MySQL is not supported.');
```

You can also verify one or more exceptions within your test function using the [toThrow()](/docs/expectations#expect-toThrow) method of the expectation API.

```php
it('throws exception', function () {
    expect(fn() => throw new Exception('Something happened.'))->toThrow(Exception::class);

    expect(fn() => throw new Exception('Something happened.'))->toThrow(Exception::class);
});
```

---

After learning how to write expectations, the next step is to explore "Test Filtering". This feature allows you to efficiently run specific tests based on criteria like test name, dirty files, etc: [Filtering Tests →](/docs/filtering-tests)
