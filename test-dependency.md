---
title: Test Dependency
description: Test Dependency
---

# Test Dependency

- [Overview](#overview)
- [Using it() function](#using-it-function)
- [Returning values](#returning-values)
- [Multiple dependencies](#multiple-dependencies)

<a name="overview"></a>

## Overview

Tests often depend on a set of preconditions or events to happen before they run, otherwise they fail.

For example, you can only assert that users can edit their accounts if first you assert that an account can be created.

Pest provides the method `depends()` to indicate that a Child test depends on one or more Parent tests.

Let's see how it works:

```php
test('Parent', function () {
    expect(true)->toBeTrue();

    $this->assertTrue(true);
});

test('Child', function () {
    expect(false)->toBeFalse();
})->depends('Parent');
```

In the example above, the Child test depends on the Parent test.

If the Parent test passes, the Child test will be executed:

```plain
   PASS  Tests\Unit\ExampleTest
  ✓ Parent
  ✓ Child

  Tests:  2 passed
```

However, if the Parent test fails, the Child test will be skipped with a user-friendly message.

```php
test('Parent', function () {
    expect(true)->toBeFalse();
});

test('Child', function () {
    expect(false)->toBeFalse();
})->depends('Parent');
```

The example above results in:

```plain
   FAIL  Tests\Unit\ExampleTest
  ⨯ Parent
  ! Child → This test depends on "P\Tests\Unit\ExampleTest::Parent" which does not exist.
```

<a name="using-it-function"></a>

## Using it() function

 The `it()` function automatically appends "it" to the test name.

For this reason, you must add  "`it `" when referencing the test name in `depends()`.

```php
it('is the Parent', function () {
    expect(true)->toBeTrue();
});

test('Child', function () {
    expect(false)->toBeFalse();
})->depends('it is the Parent');
```

Results is:

```plain
   PASS  Tests\Unit\ExampleTest
  ✓ it is the Parent
  ✓ Child

  Tests:  2 passed
```

<a name="returning-values"></a>

## Returning values

Parent tests may return values which will be accessible as function arguments in the Child test.

```php
test('Parent', function () {
    expect(true)->toBeTrue();

    return 'coming from Parent';
});

test('Child', function ($parentValue) {
    var_dump(func_get_args());

    expect($parentValue)->toBe('coming from Parent');
})->depends('Parent');
```

Resulting in:

```plain
array(1) {
  [0] =>
  string(18) "coming from Parent"
}

   PASS  Tests\Unit\ExampleTest
  ✓ Parent
  ✓ Child

  Tests:  2 passed
```

<a name="multiple-dependencies"></a>

## Multiple dependencies

You may also add multiple dependencies to a test.

All parent tests must pass, and the value returned by each test will be accessible via function parameters in order of dependency.


```php
test('Test A', function () {
    expect(true)->toBeTrue();

    return 'coming from Test A';
});

test('Test B', function () {
    expect(true)->toBeTrue();

    return 'coming from Test B';
});

test('Test C', function () {
    expect(true)->toBeTrue();

    return 'coming from Test C';
});

test('Last Test', function ($testA, $testC, $testB) {
    var_dump(func_get_args());

    expect($testA)->toBe('coming from Test A');
    expect($testC)->toBe('coming from Test C');
    expect($testB)->toBe('coming from Test B');
})->depends('Test A', 'Test C', 'Test B');
```

Resulting in:

```plain
array(3) {
  [0] =>
  string(18) "coming from Test A"
  [1] =>
  string(18) "coming from Test C"
  [2] =>
  string(18) "coming from Test B"
}

   PASS  Tests\Unit\ExampleTest
  ✓ Test C
  ✓ Test B
  ✓ Test A
  ✓ Last Test

  Tests:  4 passed
```

Next section: [Skipping Tests →](/docs/skipping-tests)
