---
title: Exceptions & Errors
description: Exceptions & Errors
---

# Exceptions & Errors

- [Overview](#overview)
- [Asserting Exception](#asserting-exception)
- [Asserting multiple Exceptions](#asserting-multiple-exceptions)
- [Conditionally Asserting Exception](#conditionally-asserting-exception)

<a name="overview"></a>

## Overview

Testing the behavior in PHP sometimes requires checking if an exception/error was thrown.

Pest provides the method `throws()`, which can be chained to your [Test function](/docs/pest-tests), to assert that an Exception was thrown.

<a name="asserting-exception"></a>

## Asserting Exception

The next example asserts an `Exception::class` was thrown during testing.

```php
it('throws exception', function () {
    throw new Exception('Something happened.');
})->throws(Exception::class);
```

To Assert the Exception `message`, pass a second argument to `throws()` containing the expected message.

```php
it('throws exception', function () {
    throw new Exception('Something happened.');
})->throws(Exception::class, 'Something happened.');
```

If you're only interested in asserting the message, you can just provide the message by itself:

```php
it('throws exception', function () {
    throw new Exception('Something happened.');
})->throws('Something happened.');
```

<a name="asserting-multiple-exceptions"></a>

## Asserting multiple Exceptions

You may use the [`toThrow()`](/docs/expectations#expect-toThrow) Expectation method to assert more than one Exception in the same test.

<a name="conditionally-asserting-exception"></a>

## Conditionally Asserting Exception

You can use the method `throwsIf()` to assert if an Exception is thrown based on a condition.

The Exception will be expected when the condition evaluates to `true`. As with `throw()` you can pass an Exception type, a message or only one of these two options.

For the next example, let's say that your test only works with the version 3.0+ of your Database.  Any inferior version would result in an Exception.

```php
it('tests APP V3.0+', function () {
    expect(true)->toBeTrue();

    // Something is only compatible with DB version 3+
    if (getenv('DB_VERSION')  < 3) {
        throw new Exception('You need to use DB 3.0+');
    }
})->throwsIf(getenv('DB_VERSION') < 3, Exception::class, 'You need to use DB 3.0+');
```

To simulate these two scenarios, let's run the Pest binary passing an environment value:

```shell
# No Exception will be thrown, it's version 3.
DB_VERSION=3 ./vendor/bin/pest

# An exception will be thrown and satisfiying your check.
DB_VERSION=2 ./vendor/bin/pest
```

As expected, both tests are passing.



---

Next section: [Groups Of Tests →](/docs/groups)
