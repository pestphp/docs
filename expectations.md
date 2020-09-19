---
title: Expectations
description: Expectations
---

# Expectations

- [Introduction](#introduction)
- [Available Expectations](#available-expectations)

<a name="introduction"></a>
## Introduction

In addition to assertions, Pest offers you a set of expectations.
These functions let you test your values against certain conditions.
This API is inspired by [Jest](https://jestjs.io/docs/expect).
Expectations also allow you to write your tests like you would a natural sentence:

```php
test('expect true to be true', function () {
  // assertion
  $this->assertTrue(true);

  // expectation
  expect(true)->toBe(true);
});
```

<a name="expect"></a>
### `expect($value)`

Start the expectation by passing your value to this function.
Then, you can chain your check(s):

```php
expect($value)->// chain your checks here
```

<style>
    .collection-method-list a {
        display: block;
    }
</style>

<a name="available-expectations"></a>
## Available Expectations

<div class="collection-method-list" markdown="1">

[`and($value)`](#expect-and)
[`not`](#expect-not)
[`toBe()`](#expect-toBe)
[`toBeEmpty()`](#expect-toBeEmpty)
[`toBeTrue()`](#expect-toBeTrue)
[`toBeFalse()`](#expect-toBeFalse)
[`toBeGreaterThan()`](#expect-toBeGreaterThan)
[`toBeGreaterThanOrEqual()`](#expect-toBeGreaterThanOrEqual)
[`toBeLessThan()`](#expect-toBeLessThan)
[`toBeLessThanOrEqual()`](#expect-toBeLessThanOrEqual)
[`toContain()`](#expect-toContain)
[`toHaveCount()`](#expect-toHaveCount)
[`toHaveProperty()`](#expect-toHaveProperty)
[`toMatchObject()`](#expect-toMatchObject)
[`toEqual()`](#expect-toEqual)
[`toEqualCanonicalizing()`](#expect-toEqualCanonicalizing)
[`toEqualWithDelta()`](#expect-toEqualWithDelta)
[`toBeInfinite()`](#expect-toBeInfinite)
[`toBeInstanceOf()`](#expect-toBeInstanceOf)
[`toBeInstanceOf()`](#expect-toBeInstanceOf)
[`toBeCallable()`](#expect-toBeCallable)
[`toBeFloat()`](#expect-toBeFloat)
[`toBeInt()`](#expect-toBeInt)
[`toBeIterable()`](#expect-toBeIterable)
[`toBeNumeric()`](#expect-toBeNumeric)
[`toBeObject()`](#expect-toBeObject)
[`toBeResource()`](#expect-toBeResource)
[`toBeScalar()`](#expect-toBeScalar)
[`toBeString()`](#expect-toBeString)
[`toBeNan()`](#expect-toBeNan)
[`toBeNull()`](#expect-toBeNull)
[`toHaveKey()`](#expect-toHaveKey)
[`toHaveKeys()`](#expect-toHaveKeys)
[`toBeReadableDirectory()`](#expect-toBeReadableDirectory)
[`toBeWritableDirectory()`](#expect-toBeWritableDirectory)
[`toStartWith()`](#expect-toStartWith)
[`toEndWith()`](#expect-toEndWith)
</div>


<a name="expect-and"></a>
### `and($value)`

Pass a new value to the `and` function to chain multiple expectations in a single test:

```php
expect($id)->toBe(14)->and($name)->toBe('Nuno');
```

<a name="expect-not"></a>
### `not`

Use the `not` modifier before a check to invert it:

```php
expect($id)->not->toBe(14);
```

<a name="expect-toBe"></a>
### `toBe()`

Asserts that two variables have the same type and value.
Used on objects, it asserts that the two variables reference the same object:

```php
expect($color)->toBe('pink');

expect($user)->toBe($user);
```

<a name="expect-toBeEmpty"></a>
### `toBeEmpty()`

Asserts that the value is empty:

```php
expect($lastSeen)->toBeEmpty();
```

<a name="expect-toBeTrue"></a>
### `toBeTrue()`

Asserts that the value is true:

```php
expect($published)->toBeTrue();
```

<a name="expect-toBeFalse"></a>
### `toBeFalse()`

Asserts that the value is false:

```php
expect($archived)->toBeFalse();
```

<a name="expect-toBeGreaterThan"></a>
### `toBeGreaterThan($expected)`

Asserts that the value is greater than the expected one:

```php
expect($age)->toBeGreaterThan(20);
```

<a name="expect-toBeGreaterThanOrEqual"></a>
### `toBeGreaterThanOrEqual($expected)`

Asserts that the value is greater than or equal to the expected one:

```php
expect($age)->toBeGreaterThanOrEqual(21);
```

<a name="expect-toBeLessThan"></a>
### `toBeLessThan($expected)`

Asserts that the value is lesser than the expected one:

```php
expect($count)->toBeLessThan(3);
```

<a name="expect-toBeLessThanOrEqual"></a>
### `toBeLessThanOrEqual($expected)`

Asserts that the value is lesser than or equal to the expected one:

```php
expect($count)->toBeLessThanOrEqual(2);
```

<a name="expect-toContain"></a>
### `toContain($needle)`

Asserts that the needle is an element of the value:

```php
expect($content)->toContain('Hello World');
```

<a name="expect-toHaveCount"></a>
### `toHaveCount(int $count)`

Asserts that the `$count` matches the number of elements of `$value`:

```php
expect($dozen)->toHaveCount(12);
```

<a name="expect-toHaveProperty"></a>
### `toHaveProperty(string $name, $value = null)`

Asserts that the `$value` contains the property `$name`:

```php
expect($user)->toHaveProperty('name');
expect($user)->toHaveProperty('name', 'Nuno');
```

<a name="expect-toMatchObject"></a>
### `toMatchObject($object)`

Asserts that the `$value` object matches a subsetof the properties of an given `$object`:

```php
$user = new stdClass();
$user->id = 1;
$user->email = 'enunomaduro@gmail.com';
$user->name = 'Nuno';

expect($user)->toMatchObject([
    'email' => 'enunomaduro@gmail.com',
    'name' => 'Nuno'
]);
```

<a name="expect-toEqual"></a>
### `toEqual($expected)`

Asserts that two variables have the same value.

```php
expect($title)->toEqual('Hello World');
```

<a name="expect-toEqualCanonicalizing"></a>
### `toEqualCanonicalizing($expected)`

Asserts that two variables have the same value.
The contents of `$value` and `$expected` are canonicalized before
they are compared. For instance, when the two variables are arrays,
then these arrays are sorted before they are compared.
When they are objects, each object is converted to an array
containing all private, protected and public attributes.

```php
expect([4, 2, 1])->toEqualCanonicalizing([2, 4, 1]);

// this is the equivalent of:
expect([1, 2, 4])->toEqual([1, 2, 4]);
```

<a name="expect-toEqualWithDelta"></a>
### `toEqualWithDelta($expected, float $delta)`

Asserts that the absolute difference between `$value` and
`$expected` is lower than `$delta`:

```php
// Pass
expect(14)->toEqualWithDelta(10, 5);

// Fail
expect(14)->toEqualWithDelta(10, 0.1);
```

<a name="expect-toBeInfinite"></a>
### `toBeInfinite()`

Asserts that the value is infinite:

```php
expect($universe)->toBeInfinite();
```

<a name="expect-toBeInstanceOf"></a>
### `toBeInstanceOf($class)`

Asserts that the value is an instance of `$class`:

```php
expect($user)->toBeInstanceOf(User::class);
```

<a name="expect-toBeArray"></a>
### `toBeArray()`

Asserts that the value is an array:

```php
expect($vegetables)->toBeArray();
```

<a name="expect-toBeInstanceOf"></a>
### `toBeInstanceOf()`

Asserts that the value is of type bool:

```php
expect($isActive)->toBeBool();
```

<a name="expect-toBeCallable"></a>
### `toBeCallable()`

Asserts that the value is of type callable:

```php
expect($controller)->toBeCallable();
```

<a name="expect-toBeFloat"></a>
### `toBeFloat()`

Asserts that the value is of type float:

```php
expect($height)->toBeFloat();
```

<a name="expect-toBeInt"></a>
### `toBeInt()`

Asserts that the value is of type integer:

```php
expect($count)->toBeInt();
```

<a name="expect-toBeIterable"></a>
### `toBeIterable()`

Asserts that the value is of type iterable:

```php
expect($value)->toBeIterable();
```

<a name="expect-toBeNumeric"></a>
### `toBeNumeric()`

Asserts that the value is of type numeric:

```php
expect($age)->toBeNumeric();
```

<a name="expect-toBeObject"></a>
### `toBeObject()`

Asserts that the value is of type object:

```php
expect($post)->toBeObject();
```

<a name="expect-toBeResource"></a>
### `toBeResource()`

Asserts that the value is of type resource:

```php
expect($resource)->toBeResource();
```

<a name="expect-toBeScalar"></a>
### `toBeScalar()`

Asserts that the value is of type scalar:

```php
expect($scalar)->toBeScalar();
```

<a name="expect-toBeString"></a>
### `toBeString()`

Asserts that the value is of type string:

```php
expect($string)->toBeString();
```

<a name="expect-toBeNan"></a>
### `toBeNan()`

Asserts that the value is not a number (NaN):

```php
expect($nan)->toBeNan();
```

<a name="expect-toBeNull"></a>
### `toBeNull()`

Asserts that the value is null:

```php
expect(null)->toBeNull();
```

<a name="expect-toHaveKey"></a>
### `toHaveKey(string $key)`

Asserts that the value array contains the provided `$key`:

```php
expect($array)->toHaveKey('key-a');
```

<a name="expect-toHaveKeys"></a>
### `toHaveKeys(array $keys)`

Asserts that the value array contains the provided `$keys`:

```php
expect($array)->toHaveKeys(['key-a', 'key-b']);
```

<a name="expect-toBeDirectory"></a>
### `toBeDirectory()`

Asserts that the value is a directory:

```php
expect($dir)->toBeDirectory();
```

<a name="expect-toBeReadableDirectory"></a>
### `toBeReadableDirectory()`

Asserts that the value is a directory and is readable.

```php
expect($dir)->toBeReadableDirectory();
```

<a name="expect-toBeWritableDirectory"></a>
### `toBeWritableDirectory()`

Asserts that the value is a directory and is writable:

```php
expect($dir)->toBeWritableDirectory();
```

<a name="expect-toStartWith"></a>
### `toStartWith(string $expected)`

Asserts that the value starts with the provided string:

```php
expect($content)->toStartWith('Hello');
```

<a name="expect-toEndWith"></a>
### `toEndWith(string $expected)`

Asserts that the value ends with the provided string:

```php
expect($content)->toEndWith('World');
```


Next section: [Setup And Teardown →](/docs/setup-and-teardown)
