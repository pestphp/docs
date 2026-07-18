---
title: Custom Expectations
description: When you find yourself writing the same expectations repeatedly between tests, you may extract them into convenient custom expectations that meet your specific requirements.
---

# Custom Expectations

Pest's expectation API is powerful by default. However, there may be times when you need to write the same expectations repeatedly between tests. In such cases, creating custom expectations that meet your specific requirements can be convenient.

Custom expectations are usually defined in the `tests/Pest.php` file, but you may also organize them in a separate `tests/Expectations.php` file for better maintainability. To create a custom expectation in Pest, chain the `extend()` method onto the `expect()` function without providing any expectation value.

For example, suppose you are testing a number utility library and you need to frequently assert that numbers fall within a given range. In this case, you may create a custom expectation called `toBeWithinRange()`:

```php
// Pest.php or Expectations.php
expect()->extend('toBeWithinRange', function (int $min, int $max) {
    return $this->toBeGreaterThanOrEqual($min)
                ->toBeLessThanOrEqual($max);
});

// Tests/Unit/ExampleTest.php
test('numeric ranges', function () {
    expect(100)->toBeWithinRange(90, 110);
});
```

While users typically rely on Pest's built-in expectations within their custom expectations, as demonstrated in the `toBeWithinRange()` example, there may be times when you need to access the expectation value directly to perform your own custom logic. In such cases, you may access the value that was passed to `expect($value)` via the `$this->value` property:

```php
expect()->extend('toBeWithinRange', function (int $min, int $max) {
    echo $this->value; // 100
});
```

Of course, you may want users to have the ability to "chain" expectations together with your custom expectation. To achieve this, ensure your custom expectation includes a `return $this` statement:

```php
// Pest.php or Expectations.php
expect()->extend('toBeWithinRange', function (int $min, int $max) {
    // Assertions based on `$this->value` and the given arguments...

    return $this; // Return this, so other expectations may chain onto this one...
});

// Tests/Unit/ExampleTest.php
test('numeric ranges', function () {
    expect(100)
        ->toBeInt()
        ->toBeWithinRange(90, 110)
        ->to...
});
```

Sometimes you may need to trigger a test failure within your custom expectation. To do so, use the `test()` method in combination with the [`fail()`](/docs/exceptions) method:

```php
// Pest.php or Expectations.php
expect()->extend('toBeDivisibleBy', function (int $divisor) {
    if ($divisor === 0) {
        test()->fail('The divisor cannot be 0.');
    }

    return expect($this->value % $divisor)->toBe(0);
});

// Tests/Unit/ExampleTest.php
test('numeral division', function () {
    expect(10)->toBeDivisibleBy(2); // Pass
    expect(10)->toBeDivisibleBy(0); // Fail "The divisor cannot be 0."
});
```

## Intercept Expectations

Although it is considered an advanced practice, you may override existing expectations with your own implementation via the `intercept()` method. When you use this method, the existing expectation will be fully substituted if the expectation value is of the specified type. For example, you may replace the `toBe()` expectation to check whether two objects of the `Illuminate\Database\Eloquent\Model` type have the same `id`:

```php
use Illuminate\Database\Eloquent\Model;
use App\Models\User;

// tests/Pest.php or tests/Expectations.php
expect()->intercept('toBe', Model::class, function(Model $expected) {
    expect($this->value->id)->toBe($expected->id);
});

// tests/Feature/ExampleTest.php
test('models', function () {
    $userA = User::find(1);
    $userB = User::find(1);

    expect($userA)->toBe($userB);
});
```

Instead of passing a string type as the second argument to the `intercept()` method, you may also pass a closure, which will be invoked to determine whether or not to override the core expectation:

```php
expect()->intercept('toBe', fn (mixed $value) => is_string($value), function (string $expected, bool $ignoreCase = false) {
    if ($ignoreCase) {
        assertEqualsIgnoringCase($expected, $this->value);
    } else {
        assertSame($expected, $this->value);
    }
});
```

## Pipe Expectations

Sometimes you may wish to run one of Pest's built-in expectations, yet include customized logic under certain conditions. In these cases, you may use the `pipe()` method. For example, we may want to customize the behavior of the `toBe()` expectation if the given value is an Eloquent model:

```php
use Illuminate\Database\Eloquent\Model;
use App\Models\User;

expect()->pipe('toBe', function (Closure $next, mixed $expected) {
    if ($this->value instanceof Model) {
        return expect($this->value->id)->toBe($expected->id);
    }

    return $next(); // Run the original, built-in expectation...
});
```

---

As demonstrated, creating custom expectations can significantly simplify your code by eliminating the need to duplicate the logic to verify that your tests are behaving as anticipated. Next, let's explore how to isolate the code under test by mocking its dependencies: [Mocking](/docs/mocking)
