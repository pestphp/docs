---
title: Skipping Tests
description: Skipping Tests
---

# Skipping Tests

- [Overview](#overview)
- [Running a single test](#running-single-test)
- [Incomplete tests](#incomplete-tests)

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

You may use a callable for the condition, which has access to the underlying test case:
```php
it('has home', function () {
    // ..
})->skip(fn() => DB::getDriverName() === 'mysql', 'Only runs when using mysql');
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

> Please be aware that `->only()` requires all tests to be written with Pest test functions to work correctly.

<a name="incomplete-tests"></a>
## Incomplete tests

If you’d like to remind yourself to come back and write a test later, just
omit the closure expression to define a pending test:

```php
it('has home');
```

Behind the scenes, Pest will mark this test as incomplete.

---

Next section: [Datasets →](/docs/datasets)
