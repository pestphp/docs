---
title: Skipping Tests
description: Skipping Tests
---

# Skipping Tests

- [Overview](#overview)
- [Conditional Skipping](#conditional-skipping)
- [Skipping using Helper Functions](#skipping-using-helper-functions)
- [Skipping based on Dataset values](#skipping-based-on-Dataset-values)
- [Incomplete tests](#incomplete-tests)

<a name="overview"></a>

## Overview

During development, you may want to temporarily turn off a test.

Rather than commenting out code, you can use the `skip()` method to prevent a test from running.

```php
it('has a homepage', function () {
    //...
})->skip();
```

It also works with [Higher Order Tests](/docs/higher-order-tests):

```php
it('works with higher order testing')
    ->assertTrue(true)
    ->skip();
```

Of course, you can also mention the reason for skipping the test:

```php
it('has a homepage', function () {
    //...
})->skip('Home page not available');
```

Resulting in:

```shell
   WARN  Tests\Unit\MyTest
  - it has a homepage → Home page not available

  Tests:  1 skipped, 1 passed
```

> The `skip()` method is the equivalent of `markTestSkipped` in PHPUnit.

<a name="conditional-skipping"></a>

## Conditional Skipping

You can pass a condition in the first parameter of the `skip()` method.

The test will be skipped whenever the condition evaluates to `true`.

```php
test('some Solaris OS particularity', function () {
    //...
})->skip(PHP_OS_FAMILY !== 'Solaris', 'Only runs on Solaris OS');
```

> Note to Laravel users: skip() runs before the Framework has booted up and does not have access to Laravel features. Use closures instead.

You may also pass a `closure` instead of a condition. Closures have access to the [underlying Test Case](/docs/underlying-test-case).

```php
use Illuminate\Support\Facades\DB;

it('has a homepage', function () {
    //...
})->skip(fn() => DB::getDriverName() !== 'mysql', 'Only runs when using MySQL');
```

<a name="skipping-using-helper-functions"></a>

## Skipping using Helper Functions

To promote code reuse and improve readability, you can extract your skipping condition into a [Custom Helper](/docs/helpers) function.

The test in the next example should run only in `PHP 8.2.5+`:

```php
it('has a homepage', function () {
    //...
})->requiresPHP('8.2.5');
```

The custom helper `requiresPHP()` is defined inside `tests/Pest.php` file, and it compares the given PHP version against the installed version.

The test is skipped if the version requirement is not met.

```php
// tests/Pest.php
<?php

function requiresPHP(string $minVersion)
{
    if (version_compare(PHP_VERSION, $minVersion, '<')) {
        test()->markTestSkipped('This test requires PHP ' . $minVersion);
    }

    return test();
}
```

<a name="skipping-based-on-Dataset-values"></a>

## Skipping based on Dataset values

Sometimes you might want to skip a test based on a certain [Dataset](/docs/datasets) value.

```php
it('reaches external API', function (string $host) {
    if ($host === 'api.bar.com' && $_ENV['api_bar_token'] === null) {
        test()->markTestSkipped('Token is not configured');
    }
    //...
})->with(['api.foo.com', 'api.bar.com', 'api.baz.com']);
```

<a name="incomplete-tests"></a>

## Incomplete tests

To define a pending test, just omit the closure expression. Pest will mark this test as incomplete.

This works as a reminder to yourself to come back later and finish writing this test.

```php
it('has a homepage');
```

---

Next section: [Datasets →](/docs/datasets)
