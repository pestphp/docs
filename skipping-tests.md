---
title: Skipping Tests
description: Skipping Tests
---

# Skipping Tests

- [Overview](#overview)
- [Running a single test](#running-single-test)
- [Writing a Pending Test](#writing-pending-test)

<a name="overview"></a>
## Overview

During development, you may want to temporarily turn off a test. Rather than commenting it out,
you can use the `skip` method.

> This is the equivalent of `markTestSkipped` in PHPUnit.
```php
it('has home', function () {
    // ..
})->skip();
```

Of course, you can also mention the reason for skipping this test:
```php
it('has home', function () {
    // ..
})->skip('Home page not available');
```

Also, you may want to skip a test depending on a condition:
```php
it('has home', function () {
    // ..
})->skip(true === true, 'Home page not available');
```

And it also works with higher order tests:
```php
it('works with higher order testing')
    ->assertTrue(true)
    ->skip();
```

<a name="running-single-test"></a>
## Running a single test

If you’d like to run a single test to debug a problem, just use the following syntax:

```php
it('has home', function () {
    // ..
})->only();
```

> Please be aware that `->only()` requires all tests to be written with Pest test functions in order to work correctly.

<a name="writing-pending-test"></a>
## Writing a Pending Test

If you’d like to remind yourself to come back and write a test later, just
omit the closure expression to define a pending test:

```php
it('has home');
```

Behind the scenes, Pest will mark this test as risky as it does not perform any assertion.

---

Next section: [Datasets →](/docs/datasets)
