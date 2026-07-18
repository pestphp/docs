---
title: Exceptions
description: When you need to check that your code throws an exception or error, Pest's throws() method makes it painless.
---

# Exceptions

When testing behavior in PHP, you may need to check whether an exception or error has been thrown. To write a test that expects an exception, you may use the `throws()` method:

```php
it('throws exception', function () {
    throw new Exception('Something happened.');
})->throws(Exception::class);
```

If you would also like to assert against the exception message, you may provide a second argument to the `throws()` method:

```php
it('throws exception', function () {
    throw new Exception('Something happened.');
})->throws(Exception::class, 'Something happened.');
```

If the exception type is not relevant and you are only concerned with the message, you may pass the message on its own, without specifying the exception's type:

```php
it('throws exception', function () {
    throw new Exception('Something happened.');
})->throws('Something happened.');
```

You may use the `throwsIf()` method to verify an exception conditionally, when a given boolean expression evaluates to true:

```php
it('throws exception', function () {
    //
})->throwsIf(fn() => DB::getDriverName() === 'mysql', Exception::class, 'MySQL is not supported.');
```

Similarly, you may use the `throwsUnless()` method to verify an exception conditionally, when a given boolean expression evaluates to false:

```php
it('throws exception', function () {
    //
})->throwsUnless(fn() => DB::getDriverName() === 'mysql', Exception::class, 'Only MySQL is supported.');
```

You may also verify that a given closure throws one or more exceptions using the [`toThrow()`](/docs/expectations#expect-toThrow) method of the expectation API:

```php
it('throws exception', function () {
    expect(fn() => throw new Exception('Something happened.'))->toThrow(Exception::class);
});
```

If you expect no exceptions to be thrown, you may use the `throwsNoExceptions()` method:

```php
it('throws no exceptions', function () {
    $result = 1 + 1;
})->throwsNoExceptions();
```

Sometimes you may wish to mark a test as failed. To accomplish this, you may use the `fail()` method:

```php
it('fails', function () {
    $this->fail();
});
```

You may also provide a message to the `fail()` method:

```php
it('fails', function () {
    $this->fail('Something went wrong.');
});
```

In addition, you may use the `fails()` method to verify that a test fails:

```php
it('fails', function () {
    $this->fail('Something happened.');
})->fails();
```

You may also assert the failure reason by providing a message to the `fails()` method:

```php
it('fails as expected', function () {
    $this->fail('Something happened.');
})->fails('Something happened.'); // Pass

it('fails in an unexpected way', function () {
    $this->fail('Something unexpected happened.');
})->fails('Something happened.'); // Fail
```

---

Now that you know how to write tests that assert exceptions, the next step is to explore test filtering, which allows you to run specific tests based on criteria such as the test name, dirty files, and more: [Filtering Tests →](/docs/filtering-tests)
