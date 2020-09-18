---
title: Assertions
description: Assertions
---

# Assertions

By now you've caught a glimpse of some of the available assertions. They
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

For the full list of **assertions**, please refer to [PHPUnit Assertions](https://phpunit.readthedocs.io/en/9.0/assertions.html) documentation.

### `assertTrue()`

The `assertTrue` asserts the given value is truthy.

```php
$this->assertTrue(true);
```

### `assertFalse()`

The `assertFalse` asserts the given value is falsy.

```php
$this->assertFalse(false);
```

### `assertCount()`

The `assertCount` asserts the given iterable to contain the same number of items.

```php
$array = [1, 2, 3, 4];

$this->assertCount(4, $array);
```

### `assertEquals()`

The `assertEquals` asserts the given values are equal.

```php
$array = [1, 2, 3, 4];

$this->assertEquals([1, 2, 3, 4], $array);
```

### `assertEmpty()`

The `assertEmpty` asserts the given iterable is empty.

```php
$array = [];

$this->assertEmpty($array);
```

### `assertStringContainsString()`

The `assertStringContainsString` asserts the given string exists.

```php
$this->assertStringContainsString('Star', 'Star Wars');
```

Remember, for the full list of **assertions**, please refer to [PHPUnit Assertions](https://phpunit.readthedocs.io/en/9.0/assertions.html) documentation.

Next section: [Expectations →](/docs/expectations)
