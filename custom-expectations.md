---
title: Custom Expectations
description: Pest's expectation API is powerful by default, but there may be times when you need to write the same expectations repeatedly between tests. In such cases, creating custom expectations that meet your specific requirements can be incredibly useful.
---

# Custom Expecatations

Pest's expectation API is powerful by default, but there may be times when you need to write the same expectations repeatedly between tests. In such cases, creating custom expectations that meet your specific requirements can be incredibly useful.

Custom expectations are usually defined in the `tests/Pest.php` file, but you can also organize them in a separate `tests/Expectation.php` file for better maintainability. To create a custom expectation in Pest, you can chain the `extend` method onto the `expect` function without providing any expectation value.

As an example, suppose you are testing a number utility library and you need to frequently assert that numbers fall within certain ranges. In such cases, you can create a custom expectation called `toBeWithinRange`:

```php
// Pest.php or Expectations.php...
expect()->extend('toBeWithinRange', function (int $min, int $max) {
    return $this->toBeGreaterThanOrEqual($min)
                ->toBeLessThanOrEqual($max);
});

// Tests/Unit/ExampleTest.php
test('numeric ranges', function () {
    expect(100)->toBeWithinRange(90, 110);
});
```

While users typically chain expectations as demonstrated in the `toBeWithinRange` example, there may be times when you need to access the expectation value directly. In such cases, you can access the expectation value, which is passed to `expect($value)`, via the `$this->value` property.

```php
expect()->extend('toBeWithinRange', function (int $min, int $max) {
    echo $this->value; // 100
});
```

Naturally, similar to any other essential expectation, you want for users to have the ability to "chain" expectations together, even with your custom expectation. To achieve this, ensure to include a `return` statement with `$this` inside the closure of your custom expectation.

```php
// Pest.php or Expectations.php...
expect()->extend('toBeWithinRange', function (int $min, int $max) {
    // Assertions based on `$this->value` and the given arguments...

    return $this; // Return this, so another expectations can chain this one...
});

// Tests/Unit/ExampleTest.php
test('numeric ranges', function () {
    expect(100)
        ->toBeInt()
        ->toBeWithinRange(90, 110)
        ->to...
});
```

Although it is considered an advanced practice, you can override existing expectations with your own implementation. This can be achieved by using the `intercept` or `pipe` method.

## Intercept Expectations

By using the `intercept` method, the existing expectation will be fully substituted if the expectation value is of the specified type. For example, you can replace the `toBe` expectation to check if two objects of the `Illuminate\Database\Eloquent\Model` type have the same `id`.

```php
use Illuminate\Database\Eloquent\Model;
use App\Models\User;

// tests/Pest.php or tests/Expectations.php
expect()->intercept('toBe', Model::class, function(Model $expected) {
    expect($this->id)->toBe($expect->id);
});

// tests/Feature/ExampleTest.php
test('models', function () {
    $userA = User::find(1);
    $userB = User::find(1);

    expect($userA)->toBe($userB);
});
```

You have the option to pass a callback as the second argument of the intercept method, which determines whether or not to override the core expectation.

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

There may be instances where you want to run the original expectation check based on certain conditions. In these cases, you can use the `pipe` method.

```php
use Illuminate\Database\Eloquent\Model;
use App\Models\User;

expect()->pipe('toBe', function (Closure $next, mixed $expected) {
    if ($this->value instanceof Model) {
        return expect($this->value->id)->toBe($expect->id);
    }

    return $next(); // continues to the original expectation...
});
```

---

As demonstrated, creating custom expectations can significantly simplify your code by eliminating the need to duplicate the logic to verify that your tests are behaving as anticipated. In the following chapter, we will explore additional CLI options that Pest provides: [CLI API Reference](/docs/cli-api-reference)
