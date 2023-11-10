---
title: Expectations
description: By setting expectations for your tests using the Pest expectation API, you can easily identify bugs and other issues in your code. This is because the API allows you to specify the expected outcome of a test, making it easy to detect any deviations from the expected behavior.
---

# Expectations

By setting expectations for your tests using the Pest expectation API, you can easily identify bugs and other issues in your code. This is because the API allows you to specify the expected outcome of a test, making it easy to detect any deviations from the expected behavior.

You can start the expectation by passing your value to the `expect($value)` function. The `expect()` function is used every time you want to test a value. You will rarely call `expect()` by itself. Instead, you will use `expect()` along with an "expectation" method to assert something about the value.

```php
test('sum', function () {
    $value = sum(1, 2);

    expect($value)->toBe(3); // Assert that the value is 3...
});
```

As demonstrated, the expect function in Pest allows you to chain multiple expectations together for a given `$value`. This means that you can perform as many checks as necessary in a single test by simply continuing to chain additional expectations.

```php
expect($value)
    ->toBeInt()
    ->toBe(3);
```

At any time, you may test the opposite of an expectation by prepending the `not` modifier to the expectation.

```php
expect($value)
    ->toBeInt()
    ->toBe(3)
    ->not->toBeString() // Not to be string...
    ->not->toBe(4); // Not to be 4...
```

With the Pest expectation API, you have access to an extensive collection of individual expectations that are designed to test various aspects of your code. Below is a comprehensive list of the available expectations.

<div class="collection-method-list" markdown="1">

- [`toBe()`](#expect-toBe)
- [`toBeArray()`](#expect-toBeArray)
- [`toBeBetween()`](#expect-toBeBetween)
- [`toBeEmpty()`](#expect-toBeEmpty)
- [`toBeTrue()`](#expect-toBeTrue)
- [`toBeTruthy()`](#expect-toBeTruthy)
- [`toBeFalse()`](#expect-toBeFalse)
- [`toBeFalsy()`](#expect-toBeFalsy)
- [`toBeGreaterThan()`](#expect-toBeGreaterThan)
- [`toBeGreaterThanOrEqual()`](#expect-toBeGreaterThanOrEqual)
- [`toBeLessThan()`](#expect-toBeLessThan)
- [`toBeLessThanOrEqual()`](#expect-toBeLessThanOrEqual)
- [`toContain()`](#expect-toContain)
- [`toContainOnlyInstancesOf()`](#expect-toContainOnlyInstancesOf)
- [`toHaveCount()`](#expect-toHaveCount)
- [`toHaveMethod()`](#expect-toHaveMethod)
- [`toHaveMethods()`](#expect-toHaveMethods)
- [`toHaveProperty()`](#expect-toHaveProperty)
- [`toHaveProperties()`](#expect-toHaveProperties)
- [`toMatchArray()`](#expect-toMatchArray)
- [`toMatchObject()`](#expect-toMatchObject)
- [`toEqual()`](#expect-toEqual)
- [`toEqualCanonicalizing()`](#expect-toEqualCanonicalizing)
- [`toEqualWithDelta()`](#expect-toEqualWithDelta)
- [`toBeIn()`](#expect-toBeIn)
- [`toBeInfinite()`](#expect-toBeInfinite)
- [`toBeInstanceOf()`](#expect-toBeInstanceOf)
- [`toBeBool()`](#expect-toBeBool)
- [`toBeCallable()`](#expect-toBeCallable)
- [`toBeFile()`](#expect-toBeFile)
- [`toBeFloat()`](#expect-toBeFloat)
- [`toBeInt()`](#expect-toBeInt)
- [`toBeIterable()`](#expect-toBeIterable)
- [`toBeNumeric()`](#expect-toBeNumeric)
- [`toBeDigits()`](#expect-toBeDigits)
- [`toBeObject()`](#expect-toBeObject)
- [`toBeResource()`](#expect-toBeResource)
- [`toBeScalar()`](#expect-toBeScalar)
- [`toBeString()`](#expect-toBeString)
- [`toBeJson()`](#expect-toBeJson)
- [`toBeNan()`](#expect-toBeNan)
- [`toBeNull()`](#expect-toBeNull)
- [`toHaveKey()`](#expect-toHaveKey)
- [`toHaveKeys()`](#expect-toHaveKeys)
- [`toHaveLength()`](#expect-toHaveLength)
- [`toBeReadableDirectory()`](#expect-toBeReadableDirectory)
- [`toBeReadableFile()`](#expect-toBeReadableFile)
- [`toBeWritableDirectory()`](#expect-toBeWritableDirectory)
- [`toBeWritableFile()`](#expect-toBeWritableFile)
- [`toStartWith()`](#expect-toStartWith)
- [`toThrow()`](#expect-toThrow)
- [`toEndWith()`](#expect-toEndWith)
- [`toMatch()`](#expect-toMatch)
- [`toMatchConstraint()`](#expect-toMatchConstraint)
- [`toBeUppercase()`](#expect-toBeUppercase)
- [`toBeLowercase()`](#expect-toBeLowercase)
- [`toBeAlpha()`](#expect-toBeAlpha)
- [`toBeAlphaNumeric()`](#expect-toBeAlphaNumeric)
- [`toBeSnakeCase()`](#expect-toBeSnakeCase)
- [`toBeKebabCase()`](#expect-toBeKebabCase)
- [`toBeCamelCase()`](#expect-toBeCamelCase)
- [`toBeStudlyCase()`](#expect-toBeStudlyCase)
- [`toHaveSnakeCaseKeys()`](#expect-toHaveSnakeCaseKeys)
- [`toHaveKebabCaseKeys()`](#expect-toHaveKebabCaseKeys)
- [`toHaveCamelCaseKeys()`](#expect-toHaveCamelCaseKeys)
- [`toHaveStudlyCaseKeys()`](#expect-toHaveStudlyCaseKeys)
- [`toHaveSameSize()`](#expect-toHaveSameSize)
- [`toBeUrl()`](#expect-toBeUrl)
- [`toBeUuid()`](#expect-toBeUuid)

</div>

In addition to the individual expectations available in Pest, the expectation API also provides several modifiers that allow you to further customize your tests. These modifiers can be used to create more complex expectations and to test multiple values at once. Here are some examples of the modifiers available in Pest:

<div class="collection-method-list" markdown="1">

- [`and()`](#expect-and)
- [`dd()`](#expect-dd)
- [`ddWhen()`](#expect-ddWhen)
- [`ddUnless()`](#expect-ddUnless)
- [`each()`](#expect-each)
- [`json()`](#expect-json)
- [`match()`](#match)
- [`not`](#expect-not)
- [`ray()`](#expect-ray)
- [`sequence()`](#expect-sequence)
- [`unless()`](#unless)
- [`when()`](#when)

</div>

---

<a name="expect-toBe"></a>
### `toBe()`

This expectation ensures that both `$value` and `$expected` share the same type and value.

If used with objects, it ensures that both variables refer to the exact same object.

```php
expect(1)->toBe(1);
expect('1')->not->toBe(1);
expect(new StdClass())->not->toBe(new StdClass());
```

<a name="expect-toBeBetween"></a>
### `toBeBetween()`

This expectation ensures that `$value` is between 2 values. It works with `int`,
`float` and `DateTime`. 

```php
expect(2)->toBeBetween(1, 3);
expect(1.5)->toBeBetween(1, 2);

$expectationDate = new DateTime('2023-09-22');
$oldestDate = new DateTime('2023-09-21');
$latestDate = new DateTime('2023-09-23');

expect($expectationDate)->toBeBetween($oldestDate, $latestDate);
```

<a name="expect-toBeEmpty"></a>
### `toBeEmpty()`

This expectation ensures that `$value` is empty.

```php
expect('')->toBeEmpty();
expect([])->toBeEmpty();
expect(null)->toBeEmpty();
```

<a name="expect-toBeTrue"></a>
### `toBeTrue()`

This expectation ensures that `$value` is true.

```php
expect($isPublished)->toBeTrue();
```

<a name="expect-toBeTruthy"></a>
### `toBeTruthy()`

This expectation ensures that `$value` is truthy.

```php
expect(1)->toBeTruthy();
expect('1')->toBeTruthy();
```

<a name="expect-toBeFalse"></a>
### `toBeFalse()`

This expectation ensures that `$value` is false.

```php
expect($isPublished)->toBeFalse();
```

<a name="expect-toBeFalsy"></a>
### `toBeFalsy()`

This expectation ensures that `$value` is falsy.

```php
expect(0)->toBeFalsy();
expect('')->toBeFalsy();
```

<a name="expect-toBeGreaterThan"></a>
### `toBeGreaterThan($expected)`

This expectation ensures that `$value` is greater than `$expected`.

```php
expect($count)->toBeGreaterThan(20);
```

<a name="expect-toBeGreaterThanOrEqual"></a>
### `toBeGreaterThanOrEqual($expected)`

This expectation ensures that `$value` is greater than or equal to `$expected`.

```php
expect($count)->toBeGreaterThanOrEqual(21);
```

<a name="expect-toBeLessThan"></a>
### `toBeLessThan($expected)`

This expectation ensures that `$value` is lesser than `$expected`.

```php
expect($count)->toBeLessThan(3);
```

<a name="expect-toBeLessThanOrEqual"></a>
### `toBeLessThanOrEqual($expected)`

This expectation ensures that `$value` is lesser than or equal to `$expected`.

```php
expect($count)->toBeLessThanOrEqual(2);
```

<a name="expect-toContain"></a>
### `toContain($needles)`

This expectation ensures that all the given needles are elements of the `$value`.

```php
expect('Hello World')->toContain('Hello');
expect('Pest: an elegant PHP Testing Framework')->toContain('Pest', 'PHP', 'Framework');
expect([1, 2, 3, 4])->toContain(2, 4);
```

<a name="expect-toContainOnlyInstancesOf"></a>
### `toContainOnlyInstancesOf($class)`

This expectation ensures that `$value` contains only instances of `$class`.

```php
$dates = [new DateTime(), new DateTime()];

expect($dates)->toContainOnlyInstancesOf(DateTime::class);
```

<a name="expect-toHaveCount"></a>
### `toHaveCount(int $count)`

This expectation ensures that the `$count` provided matches the number of elements in an iterable `$value`.

```php
expect(['Nuno', 'Luke', 'Alex', 'Dan'])->toHaveCount(4);
```

<a name="expect-toHaveMethod"></a>
### `toHaveMethod(string $name)`

This expectation ensures that `$value` has a method named `$name`.

```php
expect($user)->toHaveMethod('getFullname');
```

<a name="expect-toHaveMethods"></a>
### `toHaveMethods(iterable $names)`

This expectation ensures that `$value` has all the methods contained in `$names`.

```php
expect($user)->toHaveMethods(['getFullname', 'isAuthenticated']);
```

<a name="expect-toHaveProperty"></a>
### `toHaveProperty(string $name, $value = null)`

This expectation ensures that `$value` has a property named `$name`.

In addition, you can verify the actual value of a property by providing a second argument.

```php
expect($user)->toHaveProperty('name');
expect($user)->toHaveProperty('name', 'Nuno');
expect($user)->toHaveProperty('is_active', 'true');
```

<a name="expect-toHaveProperties"></a>
### `toHaveProperties(iterable $name)`

This expectation ensures that `$value` has property names matching all the names contained in `$names`.

```php
expect($user)->toHaveProperties(['name', 'email']);
```
In addition, you can verify the name and value of multiple properties using an associative array.

```php
expect($user)->toHaveProperties([
    'name' => 'Nuno', 
    'email' => 'enunomaduro@gmail.com'
]);
```

<a name="expect-toMatchArray"></a>
### `toMatchArray($array)`

This expectation ensures that the `$value` array matches the given `$array` subset.

```php
$user = [
    'id'    => 1,
    'name'  => 'Nuno',
    'email' => 'enunomaduro@gmail.com',
    'is_active' => true,
];

expect($user)->toMatchArray([
    'email' => 'enunomaduro@gmail.com',
    'name' => 'Nuno'
]);
```

<a name="expect-toMatchObject"></a>
### `toMatchObject($object)`

This expectation ensures that the `$value` object matches a subset of the properties of a given `$object`.

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

This expectation ensures that `$value` and `$expected` have the same value.

```php
expect($title)->toEqual('Hello World');
expect('1')->toEqual(1);
expect(new StdClass())->toEqual(new StdClass());
```

<a name="expect-toEqualCanonicalizing"></a>
### `toEqualCanonicalizing($expected)`

This expectation ensures that `$value` and `$expected` have the same values, no matter what order the elements are given in.

```php
$usersAsc = ['Dan', 'Fabio', 'Nuno'];
$usersDesc = ['Nuno', 'Fabio', 'Dan'];

expect($usersAsc)->toEqualCanonicalizing($usersDesc);
expect($usersAsc)->not->toEqual($usersDesc);
```

<a name="expect-toEqualWithDelta"></a>
### `toEqualWithDelta($expected, float $delta)`

This expectation ensures that the absolute difference between `$value` and `$expected` is lower than `$delta`.

```php
expect($durationInMinutes)->toEqualWithDelta(10, 5); //duration of 10 minutes with 5 minutes tolerance

expect(14)->toEqualWithDelta(10, 5);    // Pass
expect(14)->toEqualWithDelta(10, 0.1); // Fail
```

<a name="expect-toBeIn"></a>
### `toBeIn()`

This expectation ensures that `$value` is one of the given values.

```php
expect($newUser->status)->toBeIn(['pending', 'new', 'active']);
```

<a name="expect-toBeInfinite"></a>
### `toBeInfinite()`

This expectation ensures that `$value` is infinite.

```php
expect(log(0))->toBeInfinite();
```

<a name="expect-toBeInstanceOf"></a>
### `toBeInstanceOf($class)`

This expectation ensures that `$value` is an instance of `$class`.

```php
expect($user)->toBeInstanceOf(User::class);
```

<a name="expect-toBeArray"></a>
### `toBeArray()`

This expectation ensures that `$value` is an array.

```php
expect(['Pest','PHP','Laravel'])->toBeArray();
```

<a name="expect-toBeBool"></a>
### `toBeBool()`

This expectation ensures that `$value` is of type bool.

```php
expect($isActive)->toBeBool();
```

<a name="expect-toBeCallable"></a>
### `toBeCallable()`

This expectation ensures that `$value` is of type callable.

```php
$myFunction = function () {};

expect($myFunction)->toBeCallable();
```

<a name="expect-toBeFile"></a>
### `toBeFile()`

This expectation ensures that the string `$value` is an existing file.

```php
expect('/tmp/some-file.tmp')->toBeFile();
```

<a name="expect-toBeFloat"></a>
### `toBeFloat()`

This expectation ensures that `$value` is of type float.

```php
expect($height)->toBeFloat();
```

<a name="expect-toBeInt"></a>
### `toBeInt()`

This expectation ensures that `$value` is of type integer.

```php
expect($count)->toBeInt();
```

<a name="expect-toBeIterable"></a>
### `toBeIterable()`

This expectation ensures that `$value` is of type iterable.

```php
expect($array)->toBeIterable();
```

<a name="expect-toBeNumeric"></a>
### `toBeNumeric()`

This expectation ensures that `$value` is of type numeric.

```php
expect($age)->toBeNumeric();
expect(10)->toBeNumeric();
expect('10')->toBeNumeric();
```

<a name="expect-toBeDigits"></a>
### `toBeDigits()`

This expectation ensures that `$value` contains only digits.

```php
expect($year)->toBeDigits();
expect(15)->toBeDigits();
expect('15')->toBeDigits();
```

<a name="expect-toBeObject"></a>
### `toBeObject()`

This expectation ensures that `$value` is of type object.

```php
$object = new stdClass();

expect($object)->toBeObject();
```

<a name="expect-toBeResource"></a>
### `toBeResource()`

This expectation ensures that `$value` is of type resource.

```php
$handle = fopen('php://memory', 'r+');

expect($handle)->toBeResource();
```

<a name="expect-toBeScalar"></a>
### `toBeScalar()`

This expectation ensures that `$value` is of type scalar.

```php
expect('1')->toBeScalar();
expect(1)->toBeScalar();
expect(1.0)->toBeScalar();
expect(true)->toBeScalar();
expect([1, '1'])->not->toBeScalar();
```

<a name="expect-toBeString"></a>
### `toBeString()`

This expectation ensures that `$value` is of type string.

```php
expect($string)->toBeString();
```

<a name="expect-toBeJson"></a>
### `toBeJson()`

This expectation ensures that `$value` is a JSON string.

```php
expect('{"hello":"world"}')->toBeJson();
```

<a name="expect-toBeNan"></a>
### `toBeNan()`

This expectation ensures that `$value` is not a number (NaN).

```php
expect(sqrt(-1))->toBeNan();
```

<a name="expect-toBeNull"></a>
### `toBeNull()`

This expectation ensures that `$value` is null.

```php
expect(null)->toBeNull();
```

<a name="expect-toHaveKey"></a>
### `toHaveKey(string $key)`

This expectation ensures that `$value` contains the provided `$key`.

```php
expect(['name' => 'Nuno', 'surname' => 'Maduro'])->toHaveKey('name');
expect(['name' => 'Nuno', 'surname' => 'Maduro'])->toHaveKey('name', 'Nuno');
expect(['user' => ['name' => 'Nuno', 'surname' => 'Maduro']])->toHaveKey('user.name');
expect(['user' => ['name' => 'Nuno', 'surname' => 'Maduro']])->toHaveKey('user.name', 'Nuno');
```

<a name="expect-toHaveKeys"></a>
### `toHaveKeys(array $keys)`

This expectation ensures that `$value` contains the provided `$keys`.

```php
expect(['id' => 1, 'name' => 'Nuno'])->toHaveKeys(['id', 'name']);
expect(['message' => ['from' => 'Nuno', 'to' => 'Luke'] ])->toHaveKeys(['message.from', 'message.to']);
```

<a name="expect-toHaveLength"></a>
### `toHaveLength(int $number)`

This expectation ensures that the provided `$number` matches the length of a string `$value` or the number of elements in an iterable `$value`.

```php
expect('Pest')->toHaveLength(4);
expect(['Nuno', 'Maduro'])->toHaveLength(2);
```

<a name="expect-toBeDirectory"></a>
### `toBeDirectory()`

This expectation ensures that the string `$value` is a directory.

```php
expect('/tmp')->toBeDirectory();
```

<a name="expect-toBeReadableDirectory"></a>
### `toBeReadableDirectory()`

This expectation ensures that the string `$value` is a directory and that it is readable.

```php
expect('/tmp')->toBeReadableDirectory();
```

<a name="expect-toBeReadableFile"></a>
### `toBeReadableFile()`

This expectation ensures that the string `$value` is a file and that it is readable.

```php
expect('/tmp/some-file.tmp')->toBeReadableFile();
```

<a name="expect-toBeWritableDirectory"></a>
### `toBeWritableDirectory()`

This expectation ensures that the string `$value` is a directory and that it is writable.

```php
expect('/tmp')->toBeWritableDirectory();
```

<a name="expect-toBeWritableFile"></a>
### `toBeWritableFile()`

This expectation ensures that the string `$value` is a file and that it is writable.

```php
expect('/tmp/some-file.tmp')->toBeWritableFile();
```

<a name="expect-toStartWith"></a>
### `toStartWith(string $expected)`

This expectation ensures that `$value` starts with the provided string.

```php
expect('Hello World')->toStartWith('Hello');
```

<a name="expect-toThrow"></a>
### `toThrow()`

This expectation ensures that a closure throws a specific exception class, exception message, or both.

```php
expect(fn() => throw new Exception('Something happened.'))->toThrow(Exception::class);
expect(fn() => throw new Exception('Something happened.'))->toThrow('Something happened.');
expect(fn() => throw new Exception('Something happened.'))->toThrow(Exception::class, 'Something happened.');
expect(fn() => throw new Exception('Something happened.'))->toThrow(new Exception('Something happened.'));
```

<a name="expect-toMatch"></a>
### `toMatch(string $expression)`

This expectation ensures that `$value` matches a regular expression.

```php
expect('Hello World')->toMatch('/^hello wo.*$/i');
```

<a name="expect-toEndWith"></a>
### `toEndWith(string $expected)`

This expectation ensures that `$value` ends with the provided string.

```php
expect('Hello World')->toEndWith('World');
```

<a name="expect-toMatchConstraint"></a>
### `toMatchConstraint(Constraint $constraint)`

This expectation ensures that `$value` matches a specified PHPUnit constraint.

```php
use PHPUnit\Framework\Constraint\IsTrue;

expect(true)->toMatchConstraint(new IsTrue());
```

<a name="expect-toBeUppercase"></a>
### `toBeUppercase(string $expected)`

This expectation ensures that `$value` is uppercase.

```php
expect('PESTPHP')->toBeUppercase();
```

<a name="expect-toBeLowercase"></a>
### `toBeLowercase(string $expected)`

This expectation ensures that `$value` is lowercase.

```php
expect('pestphp')->toBeLowercase();
```

<a name="expect-toBeAlpha"></a>
### `toBeAlpha(string $expected)`

This expectation ensures that `$value` only contains alpha characters.

```php
expect('pestphp')->toBeAlpha();
```

<a name="expect-toBeAlphaNumeric"></a>
### `toBeAlphaNumeric(string $expected)`

This expectation ensures that `$value` only contains alphanumeric characters.

```php
expect('pestPHP123')->toBeAlphaNumeric();
```

<a name="expect-toBeSnakeCase"></a>
### `toBeSnakeCase()`

This expectation ensures that `$value` only contains string in snake_case format.

```php
expect('snake_case')->toBeSnakeCase();
```

<a name="expect-toBeKebabCase"></a>
### `toBeKebabCase()`

This expectation ensures that `$value` only contains string in kebab-case format.

```php
expect('kebab-case')->toBeKebabCase();
```

<a name="expect-toBeCamelCase"></a>
### `toBeCamelCase()`

This expectation ensures that `$value` only contains string in camelCase format.

```php
expect('camelCase')->toBeCamelCase();
```

<a name="expect-toBeStudlyCase"></a>
### `toBeStudlyCase()`

This expectation ensures that `$value` only contains string in StudlyCase format.

```php
expect('StudlyCase')->toBeStudlyCase();
```

<a name="expect-toHaveSnakeCaseKeys"></a>
### `toHaveSnakeCaseKeys()`

This expectation ensures that `$value` only contains an array with keys in snake_case format.

```php
expect(['snake_case' => 'abc123'])->toHaveSnakeCaseKeys();
```

<a name="expect-toHaveKebabCaseKeys"></a>
### `toHaveKebabCaseKeys()`

This expectation ensures that `$value` only contains an array with keys in kebab-case format.

```php
expect(['kebab-case' => 'abc123'])->toHaveKebabCaseKeys();
```

<a name="expect-toHaveCamelCaseKeys"></a>
### `toHaveCamelCaseKeys()`

This expectation ensures that `$value` only contains an array with keys in camelCase format.

```php
expect(['camelCase' => 'abc123'])->toHaveCamelCaseKeys();
```

<a name="expect-toHaveStudlyCaseKeys"></a>
### `toHaveStudlyCaseKeys()`

This expectation ensures that `$value` only contains an array with keys in StudlyCase format.

```php
expect(['StudlyCase' => 'abc123'])->toHaveStudlyCaseKeys();
```

<a name="expect-toHaveSameSize"></a>
### `toHaveSameSize()`

This expectation ensures that the size of `$value` and the provided iterable are the same.

```php
expect(['foo', 'bar'])->toHaveSameSize(['baz', 'bazz']);
```

<a name="expect-toBeUrl"></a>
### `toBeUrl()`

This expectation ensures that `$value` is a URL.

```php
expect('https://pestphp.com/')->toBeUrl();
```

<a name="expect-toBeUuid"></a>
### `toBeUuid()`

This expectation ensures that `$value` is an UUID.

```php
expect('ca0a8228-cdf6-41db-b34b-c2f31485796c')->toBeUuid();
```

---

<a name="expect-and"></a>
### `and($value)`

The `and()` modifier allows you to pass a new `$value`, enabling you to chain multiple expectations in a single test.

```php
expect($id)->toBe(14)
    ->and($name)->toBe('Nuno');
```

<a name="expect-dd"></a>
### `dd()`

The `dd()` modifier

Use the `dd()` modifier allows you to dump the current expectation `$value` and stop the code execution. This can be useful for debugging by allowing you to inspect the current state of the $value at a particular point in your test.

```php
expect(14)->dd(); // 14
expect([1, 2])->sequence(
    fn ($number) => $number->toBe(1),
    fn ($number) => $number->dd(), // 2
);
```

<a name="expect-ddWhen"></a>
### `ddWhen($condition)`

Use the `ddWhen()` modifier allows you to dump the current expectation `$value` and stop the code execution when the given `$condition` is truthy.

```php
expect([1, 2])->each(
    fn ($number) => $number->ddWhen(fn (int $number) => $number === 2) // 2
);
```

<a name="expect-ddUnless"></a>
### `ddUnless($condition)`

Use the `ddUnless()` modifier allows you to dump the current expectation `$value` and stop the code execution when the given `$condition` is falsy.

```php
expect([1, 2])->each(
    fn ($number) => $number->ddUnless(fn (int $number) => $number === 1) // 2
);
```

<a name="expect-each"></a>
### `each()`

The `each()` modifier allows you to create an expectation on each item of the given iterable. It works by iterating over the iterable and applying the expectation to each item.

```php
expect([1, 2, 3])->each->toBeInt();
expect([1, 2, 3])->each->not->toBeString();
expect([1, 2, 3])->each(fn ($number) => $number->toBeLessThan(4));
```

<a name="expect-json"></a>
### `json()`

The `json()` modifier decodes the current expectation `$value` from JSON to an array.

```php
expect('{"name":"Nuno","credit":1000.00}')
    ->json()
    ->toHaveCount(2)
    ->name->toBe('Nuno')
    ->credit->toBeFloat();

expect('not-a-json')->json(); //Fails
```

<a name="match"></a>
### `match()`

The `match()` modifier executes the closure associated with the first array key that matches the value of the first argument given to the method.

```php
expect($user->miles)
    ->match($user->status, [
        'new'  => fn ($userMiles) => $userMiles->ToBe(0),
        'gold'  => fn ($userMiles) => $userMiles->toBeGreaterThan(500),
        'platinum' => fn ($userMiles) => $userMiles->toBeGreaterThan(1000),
    ]);
```

To check if the expected value is equal to the value associated with the matching key, you can directly pass the expected value as the array value instead of using a closure.

```php
expect($user->default_language)
    ->match($user->country, [
        'PT' => 'Português',
        'US' => 'English',
        'TR' => 'Türkçe',
    ]);
```

<a name="expect-not"></a>
### `not`

The `not` modifier allows to invert the subsequent expectation.

```php
expect(10)->not->toBeGreaterThan(100);
expect(true)->not->toBeFalse();
```

<a name="expect-ray"></a>
### `ray()`

The `ray()` modifier allows you to debug the current `$value` with **[myray.app](https://myray.app/)**.

```php
expect(14)->ray(); // 14
expect([1, 2])->sequence(
    fn ($number) => $number->toBe(1),
    fn ($number) => $number->ray(), // 2
);
```

<a name="expect-sequence"></a>
### `sequence()`

The `sequence()` modifier allows you to specify a sequential set of expectations for a single iterable.

```php
expect([1, 2, 3])->sequence(
    fn ($number) => $number->toBe(1),
    fn ($number) => $number->toBe(2),
    fn ($number) => $number->toBe(3),
);
```

The `sequence()` modifier can also be used with associative iterables. Each closure in the sequence will receive two arguments: the first argument being the expectation for the value and the second argument being the expectation for the key.

```php
expect(['hello' => 'world', 'foo' => 'bar', 'john' => 'doe'])->sequence(
    fn ($value, $key) => $value->toEqual('world'),
    fn ($value, $key) => $key->toEqual('foo'),
    fn ($value, $key) => $value->toBeString(),
);
```

The `sequence()` modifier can also be used to check if each value in the iterable matches a set of expected values. In this case, you can pass the expected values directly to the sequence() method instead of using closures.

```php
expect(['foo', 'bar', 'baz'])->sequence('foo', 'bar', 'baz');
```

<a name="when"></a>
### `when()`

The `when()` modifier runs the provided callback when the first argument passed to the method evaluates to true.

```php
expect($user)
    ->when($user->is_verified === true, fn ($user) => $user->daily_limit->toBeGreaterThan(10))
    ->email->not->toBeEmpty();
```

<a name="unless"></a>
### `unless()`

The `unless()` modifier runs the provided callback when the first argument passed to the method evaluates to false.

```php
expect($user)
    ->unless($user->is_verified === true, fn ($user) => $user->daily_limit->toBe(10))
    ->email->not->toBeEmpty();
```

---

After learning how to write expectations, the next section in the documentation, "Hooks" covers useful functions like "beforeEach" and "afterEach" that can be used to set up preconditions and cleanup actions for your tests: [Hooks →](/docs/hooks)
