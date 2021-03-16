---
title: Setup and Teardown
description: Setup and Teardown
---

# Setup and Teardown

- [Overview](#overview)
- [Setup and Teardown](#setup-and-teardown)
- [Example](#example)

<a name="overview"></a>
## Overview

In your tests, you may want to run some code before and after each test or
file. In this section, we'll discuss the globally available
functions that allow you to do that.

<a name="setup-and-teardown"></a>
## Setup and Teardown

Here you can find a list of available setup/teardown functions.

### `beforeEach()`

The function `beforeEach()` runs the given closure before each test
in the current file.

> It's the equivalent to `setUp` in PHPUnit.

```php
<?php

beforeEach(function () {
    echo 'beforeEach';
});

test('foo', function () {
    echo 'test foo';
});

test('bar', function () {
    echo 'test bar';
});

// beforeEach
// test foo
// beforeEach
// test bar
```

As usual, the `$this` variable in the `beforeEach` function
is bound to the current Test Case object. Therefore, you can share data
with both `it` and `test` functions.

```php
beforeEach(function () {
    $this->hey = 'artisan';
});

it('has artisan', function () {
    echo $this->hey;
});

// artisan
```

### `afterEach()`

The function `afterEach()` runs the given closure after each test
in the current file.

> It's the equivalent to `tearDown` in PHPUnit.

```php
<?php

afterEach(function () {
    echo 'afterEach';
});

test('foo', function () {
    echo 'test foo';
});

test('bar', function () {
    echo 'test bar';
});

// test foo
// afterEach
// test bar
// afterEach
```

### `beforeAll()`

The function `beforeAll()` runs the given closure before
all tests in the current file.

> It's the equivalent to the `@beforeClass` annotation in PHPUnit.

```php
<?php

beforeAll(function () {
    echo 'beforeAll';
});

test('foo', function () {
    echo 'test foo';
});

test('bar', function () {
    echo 'test bar';
});

// beforeAll
// test foo
// test bar
```

### `afterAll()`

The function `afterAll()` runs the given closure after
all tests in the current file.

 > It's the equivalent to the `@afterClass` annotation in PHPUnit.

```php
<?php

afterAll(function () {
    echo 'afterAll';
});

test('foo', function () {
    echo 'test foo';
});

test('bar', function () {
    echo 'test bar';
});

// test foo
// test bar
// afterAll
```

<a name="example"></a>
## Example

To understand the order of execution of all those
functions, let's take a look at the example below:

```php
beforeAll(fn () => dump('beforeAll'));
afterAll(fn () => dump('afterAll'));

beforeEach(fn () => dump('beforeEach'));
afterEach(fn () => dump('afterEach'));

test('example 1', fn () => dump('test foo'));
test('example 2', fn () => dump('test bar'));

// "beforeAll"
// "beforeEach"
// "test foo"
// "afterEach"
// "beforeEach"
// "test bar"
// "afterEach"
// "afterAll"
```

---

Next section: [Higher Order Tests →](/docs/higher-order-tests)
