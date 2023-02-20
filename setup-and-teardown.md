---
title: Setup and Teardown
description: Setup and Teardown
---

# Setup and Teardown

- [Overview](#overview)
- [Setup and Teardown](#setup-and-teardown)
    - [beforeEach](#beforeeach)
    - [afterEach](#aftereach)
    - [beforeAll](#beforeall)
    - [afterAll](#afterall)
    - [Reusing data in Tests](#reusing-data-in-tests)
- [Reusable (Shared) Setup and Teardown](#reusable-shared-setup-and-teardown)
- [Lifecycle Demonstration](#lifecycle-demonstration)

<a name="overview"></a>

## Overview

When running tests, it may be useful to execute certain code before and after each test or file.

The terms "setup" and "teardown" may seem complex, but they can be thought of as similar to preparing ingredients, cooking, and cleaning up after a meal. In technical terms, setup functions can be considered as constructors and teardown functions as destructors.

This section will cover the globally accessible functions that enable these actions.

<a name="setup-and-teardown"></a>

## Setup and Teardown

Here you can find a list of available setup/teardown functions.

<a name="beforeeach"></a>

## `beforeEach()`

The `beforeEach()` function executes the provided closure before every test within the current file.

```php
<?php
// tests/Unit/MyTest.php

beforeEach(function () {
    echo "\n--> calls beforeEach\n";
});

test('test 1', function () {
    echo "\nrunning test 1\n";
    expect(true)->toBeTrue();
});

test('test 2', function () {
    echo "\nrunning test 2\n";
    expect(true)->toBeTrue();
});
```

Resulting in:

```shell
--> calls beforeEach
running test 1
--> calls beforeEach
running test 2

  PASS  Tests\Unit\MyTest
  ✓ test 1
  ✓ test 2

  Tests:  2 passed
```

<a name="aftereach"></a>

### `afterEach()`

The function `afterEach()` runs the given `closure` after each test in the `current file`.

Here, you can free up any resources (e.g, files, sockets) allocated during `beforeEach`.

```php
<?php
// tests/Unit/MyTest.php

afterEach(function () {
    echo "\n--> calls afterEach\n";
});

test('test 1', function () {
    echo "\nrunning test 1\n";
    expect(true)->toBeTrue();
});

test('test 2', function () {
    echo "\nrunning test 2\n";
    expect(true)->toBeTrue();
});
```

Resulting in:

```shell
running test 1
--> calls afterEach
running test 2
--> calls afterEach

  PASS  Tests\Unit\MyTest
  ✓ test 1
  ✓ test 2

  Tests:  2 passed
```

<a name="beforeall"></a>

### `beforeAll()`

The function `beforeAll()` runs the given `closure` before all tests in the `current file`.

```php
<?php
// tests/Unit/MyTest.php

beforeAll(function () {
    echo "\n--> calls beforeAll\n";
});

test('test 1', function () {
    echo "\nrunning test 1\n";
    expect(true)->toBeTrue();
});

test('test 2', function () {
    echo "\nrunning test 2\n";
    expect(true)->toBeTrue();
});
```

Resulting in:

```shell
--> calls beforeAll

running test 1

running test 2

  PASS  Tests\Unit\MyTest
  ✓ test 1
  ✓ test 2

  Tests:  2 passed
```

<a name="afterall"></a>

### `afterAll()`

The function `afterAll()` runs the given `closure` after all tests in the `current file`.

```php
<?php
// tests/Unit/MyTest.php

afterAll(function () {
    echo "\n--> calls afterAll\n";
});

test('test 1', function () {
    echo "\nrunning test 1\n";
    expect(true)->toBeTrue();
});

test('test 2', function () {
    echo "\nrunning test 2\n";
    expect(true)->toBeTrue();
});
```

Resulting in:

```shell
running test 1

running test 2

--> calls afterAll

  PASS  Tests\Unit\MyTest
  ✓ test 1
  ✓ test 2

  Tests:  2 passed
```

<a name="reusing-data-in-tests"></a>

### `Reusing data in Tests`

The variable `$this` is bound to the current `Test Case` object in both methods: `beforeEach()` and `afterEach()`.

This means that you can reuse data, sharing it with all tests in the `current file`.

```php
// tests/Unit/MyTest.php

beforeEach(function () {
    $this->number = 10;
});

test('multiplication', function () {
    expect($this->number * 10)->toBe(100);
    $this->AssertSame(100, $this->number * 10);
});

it('sums', function () {
    expect($this->number + 1)->toBe(11);
    $this->AssertSame(11, $this->number + 1);
});
```

> [Assertions](/docs/assertions) and [Expectations](/docs/expectations) behave in the same way. The example uses both for demonstration purpose.

#### A Full picture example

The next example will demonstrate some concepts. You are welcome to copy and paste this code to see it in action.

Now, let's break it down:

In `beforeEach`:

- The properties `username` and `logFile` are being set before each test (over and over again).
- These two properties are accessible for all tests inside *MyTest.php* file.
- The **test 1** modifies the `username` to "Nuno Maduro" and then, performs all its verifications.
- Before **test 2** runs, the `username` is being reset to "Nuno".
- The **test 2** performs all its verifications.

In `afterEach`:

Let's image that **test 1** and **test 2** allocate resources to write a log entry in "/logs/app.log".

Here, we clean up any leftover files and close any resources previously allocated before each test runs.

```php
<?php
// tests/Unit/MyTest.php

// Setup and Teardown

beforeEach(function () {
    echo "\n--> beforeEach is setting data\n";
    $this->username = 'Nuno';
    $this->logFile  = '/logs/app.log';
});

afterEach(function () {
    echo "\n--> afterEach: deleting log file: {$this->logFile}\n";
    // clean up files and close resources
});

// Tests

test('test 1', function () {
    echo "\nrunning test 1 (writting to {$this->logFile})\n";

    $this->username .= ' Maduro'; //Modifies the username value.

    expect($this->username)->toBe('Nuno Maduro');
});

test('test 2', function () {
    echo "\nrunning test 2 (writting to {$this->logFile})\n";

    expect($this->username)->toBe('Nuno'); //Confirms that username is again the default value.
});

```

Resulting in:

```shell
--> beforeEach is setting data

running test 1 (writting to /logs/app.log)

--> afterEach: deleting log file: /logs/app.log

--> beforeEach is setting data

running test 2 (writting to /logs/app.log)

--> afterEach: deleting log file: /logs/app.log

  PASS  Tests\Unit\MyTest
  ✓ test 1
  ✓ test 2

  Tests:  2 passed
```

<a name="reusable-shared-setup-and-teardown"></a>

## Reusable (Shared) Setup and Teardown

At some point, you may need to reuse a `setup and teardown` procedure, sharing it with more than one test file.

You can do this using the [`uses()`](/docs/underlying-test-case#uses) function in your `tests/Pest.php` file.

All methods described on this page are available for reuse/sharing, as demonstrated in the following example:

```php
// tests/Pest.php
uses()
    ->beforeAll(fn () => print("\n--> GLOBAL beforeAll\n"))
    ->beforeEach(fn () => print("\n-> GLOBAL beforeEach\n"))
    ->afterEach(fn () => print("\n-> GLOBAL afterEach\n"))
    ->afterAll(fn () => print("\n--> GLOBAL afterAll\n"))
    ->in('Unit');
```

<a name="lifecycle-demonstration"></a>

## Lifecycle Demonstration

To understand the order of execution of all those functions, let's take a look at the example below:

```php
<?php
// tests/Unit/MyTest.php

afterEach(fn () => print("\n-> afterEach\n"));
afterAll(fn () => print("\n--> afterAll\n"));

beforeAll(fn () => print("\n--> beforeAll\n"));
beforeEach(fn () => print("\n-> beforeEach\n"));

test('test 1')->tap(fn () => print("\nrunning test 1\n"))->assertTrue(true);
test('test 2')->tap(fn () => print("\nrunning test 2\n"))->assertTrue(true);
```

Resulting in:

```shell
--> beforeAll

-> beforeEach

running test 1

-> afterEach

-> beforeEach

running test 2

-> afterEach

--> afterAll

  PASS  Tests\Unit\MyTest
  ✓ test 1
  ✓ test 2

  Tests:  2 passed
```

For the next example, let's create global (reusable/shared hooks) in `tests/Pest.php`.

```php
// tests/Pest.php
<?php

uses()
    ->beforeAll(fn () => print("\n--> GLOBAL beforeAll\n"))
    ->beforeEach(fn () => print("\n-> GLOBAL beforeEach\n"))
    ->afterEach(fn () => print("\n-> GLOBAL afterEach\n"))
    ->afterAll(fn () => print("\n--> GLOBAL afterAll\n"))
    ->in('Unit');
```

And once again run the previous example:

```php
<?php
// tests/Unit/MyTest.php

afterEach(fn () => print("\n-> afterEach\n"));
afterAll(fn () => print("\n--> afterAll\n"));

beforeAll(fn () => print("\n--> beforeAll\n"));
beforeEach(fn () => print("\n-> beforeEach\n"));

test('test 1')->tap(fn () => print("\nrunning test 1\n"))->assertTrue(true);
test('test 2')->tap(fn () => print("\nrunning test 2\n"))->assertTrue(true);
```

Now, we can see the execution order when using a combination of Global and Local hooks:

```shell
--> GLOBAL beforeAll

--> beforeAll

-> GLOBAL beforeEach

-> beforeEach

running test 1

-> GLOBAL afterEach

-> afterEach

-> GLOBAL beforeEach

-> beforeEach

running test 2

-> GLOBAL afterEach

-> afterEach

--> GLOBAL afterAll

--> afterAll

  PASS  Tests\Unit\MyTest
  ✓ test 1
  ✓ test 2

  Tests:  2 passed
```

---

Next section: [Higher Order Tests →](/docs/higher-order-tests)
