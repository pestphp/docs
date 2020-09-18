---
title: Exceptions & Errors
description: Exceptions & Errors
---

# Exceptions & Errors

Testing the behavior in PHP sometimes requires
checking if an exception/error was thrown.

Writing a test to expect a test to throw an exception
can be done as follows:
```php
it('throws exception', function () {
    throw new Exception('Something happened.');
})->throws(Exception::class);
```

If you wish to assert the exception message too, you need to give
a second argument to the `throws` method.
```php
it('throws exception', function () {
    throw new Exception('Something happened.');
})->throws(Exception::class, 'Something happened.');
```

Next section: [Groups Of Tests →](/docs/groups)
