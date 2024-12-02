---
title: Exceptions
description: When testing behavior in PHP, you might need to check if an exception or error has been thrown. To create a test that expects an exception to be thrown, you can use the `throws()` method.
---

# Exceptions

When testing behavior in PHP, you might need to check if an exception or error has been thrown. To create a test that expects an exception to be thrown, you can use the `throws()` method.

```php
it('throws exception', function () {
    throw new Exception('Something happened.');
})->throws(Exception::class);
```

If you also want to make an assertion against the exception message, you may provide a second argument to the `throws()` method.

```php
it('throws exception', function () {
    throw new Exception('Something happened.');
})->throws(Exception::class, 'Something happened.');
```

If the exception type is not relevant, and you're only concerned with the message, you can simply pass the message without specifying the exception's type.

```php
it('throws exception', function () {
    throw new Exception('Something happened.');
})->throws('Something happened.');
```

You can use the `throwsIf()` method to conditionally verify an exception if a given boolean expression evaluates to true.

```php
it('throws exception', function () {
    //
})->throwsIf(fn() => DB::getDriverName() === 'mysql', Exception::class, 'MySQL is not supported.');
```

Just like `throwsIf()` method, you can use the `throwsUnless()` method to conditionally verify an exception if a given boolean expression evaluates to false.

```php
it('throws exception', function () {
    //
})->throwsUnless(fn() => DB::getDriverName() === 'mysql', Exception::class, 'Only MySQL is supported.');
```

You can also verify that a given closure throws one or more exceptions using the [toThrow()](/docs/expectations#expect-toThrow) method of the expectation API.

```php
it('throws exception', function () {
    expect(fn() => throw new Exception('Something happened.'))->toThrow(Exception::class);
});
```

If you expect no exceptions to be thrown, you can use the `throwsNoExceptions()` method.

```php
it('throws no exceptions', function () {
    $result = 1 + 1;
})->throwsNoExceptions();
```

Sometimes, you may want to simply mark a test as failed. You can use the `fail()` method to do so.

```php
it('fails', function () {
    $this->fail();
});
```

You may also provide a message to the `fail()` method.

```php
it('fails', function () {
    $this->fail('Something went wrong.');
});
```

In addition, you can also use the `fails()` method to verify if a test fails.

```php
it('fails', function () {
    $this->fail('Something happened.');
})->fails();
```

You can also assert the failure reason by providing a message to the `fails()` method.

```php
it('fails as expected', function () {
    $this->fail('Something happened.');
})->fails('Something happened.'); // Pass

it('fails in an unexpected way', function () {
    $this->fail('Something unexpected happened.');
})->fails('Something happened.'); // Fail
```

---

After learning how to write tests that assert exceptions, the next step is to explore "Test Filtering". This feature allows you to efficiently run specific tests based on criteria like test name, dirty files, and more: [Filtering Tests →](/docs/filtering-tests)
