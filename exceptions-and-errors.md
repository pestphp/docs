---
title: Exceptions & Errors
description: Exceptions & Errors
---

# Exceptions & Errors

- [Overview](#overview)

<a name="overview"></a>
## Overview

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
a second argument to the `throws` method:
```php
it('throws exception', function () {
    throw new Exception('Something happened.');
})->throws(Exception::class, 'Something happened.');
```

If you're only interested in the message, and the exception type
isn't important, you can just provide the message by itself:
```php
it('throws exception', function () {
    throw new Exception('Something happened.');
})->throws('Something happened.');
```


---

Next section: [Groups Of Tests →](/docs/groups)
