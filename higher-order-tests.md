---
title: Higher Order Tests
description: Higher Order Tests
---

# Higher Order Tests

- [Overview](#overview)

<a name="overview"></a>
## Overview

Pest also provides support for **higher order tests**, which are shortcuts for performing
common actions while writing your tests.

The best way to think about this is: If you
don't provide a closure, the chained methods are going to create a closure for
you. Here is the most basic example:

```php
test('true is true')->assertTrue(true);
```

The code above is equivalent to:
```php
test('true is true', function () {
    $this->assertTrue(true);
});
```

Pest will properly delegate to any given method and its arguments of the test itself.

> **Warning**:  You can't access runtime helpers in Laravel, like `route`, with higher-order tests.

The exact same rules apply to global functions like `beforeEach` or `afterEach`. Let's
take a look at an example using the Laravel Framework:

```php
beforeEach()->withoutMiddleware();

it('has home')
    ->get('/admin')
    ->assertSee('Hello World');
```

---

Next section: [Custom Helpers →](/docs/helpers)
