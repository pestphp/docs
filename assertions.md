---
title: Assertions
description: Assertions
---

# Assertions

- [Overview](#overview)
- [Available Assertions](#available-assertions)
    - [`assertTrue()`](#assertTrue)
    - [`assertFalse()`](#assertFalse)
    - [`assertCount()`](#assertCount)
    - [`assertEquals()`](#assertEquals)
    - [`assertEmpty()`](#assertEmpty)
    - [`assertStringContainsString()`](#assertStringContainsString)
    
<a name="overview"></a>
## Overview

By now you've caught a glimpse of some available assertions. They
are the ones that actually perform the checks to ensure that things
are going as planned.

Remember, the `$this` variable inside the given
closure in tests is always bound to a Test Case class. Therefore
assertions are methods of the `$this` variable.

```php
it('asserts true is true', function () {
    $this->assertTrue(true);
});
```

<a name="available-assertions"></a>
## Available Assertions

For the full list of **assertions**, please refer to [PHPUnit Assertions](https://phpunit.readthedocs.io/en/9.0/assertions.html) documentation.

<a name="assertTrue"></a>
### `assertTrue()`

The `assertTrue` asserts the given value is truthy.

```php
$this->assertTrue(true);
```

<a name="assertFalse"></a>
### `assertFalse()`

The `assertFalse` asserts the given value is falsy.

```php
$this->assertFalse(false);
```

<a name="assertCount"></a>
### `assertCount()`

The `assertCount` asserts the given iterable to contain the same number of items.

```php
$array = [1, 2, 3, 4];

$this->assertCount(4, $array);
```

<a name="assertEquals"></a>
### `assertEquals()`

The `assertEquals` asserts the given values are equal.

```php
$array = [1, 2, 3, 4];

$this->assertEquals([1, 2, 3, 4], $array);
```

<a name="assertEmpty"></a>
### `assertEmpty()`

The `assertEmpty` asserts the given iterable is empty.

```php
$array = [];

$this->assertEmpty($array);
```

<a name="assertStringContainsString"></a>
### `assertStringContainsString()`

The `assertStringContainsString` asserts the given string exists.

```php
$this->assertStringContainsString('Star', 'Star Wars');
```

Next section: [Expectations →](/docs/expectations)
