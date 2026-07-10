---
title: Datasets
description: Define an array of test data and Pest will run the same test for each set automatically, freeing you from repeating the same test by hand.
---

# Datasets

Datasets allow you to define an array of test data, and Pest will run the same test once for each set automatically. This saves you time and effort, freeing you from repeating the same test by hand with different data.

```php
it('has emails', function (string $email) {
    expect($email)->not->toBeEmpty();
})->with(['enunomaduro@gmail.com', 'other@example.com']);
```

When you run your tests, Pest will automatically add informative descriptions to any test that uses a dataset, outlining the parameters used in each case. This helps you understand the data at a glance and pinpoint the source if a test fails.

<div class="code-snippet">
    <img src="/assets/img/datasets-emails.webp?1" style="--lines: 3" />
</div>

Of course, you may supply multiple arguments by providing an array that contains arrays of arguments:

```php
it('has emails', function (string $name, string $email) {
    expect($email)->not->toBeEmpty();
})->with([
    ['Nuno', 'enunomaduro@gmail.com'],
    ['Other', 'other@example.com']
]);
```

To add your own description to a dataset value, you may assign it a key:

```php
it('has emails', function (string $email) {
    expect($email)->not->toBeEmpty();
})->with([
    'james' => 'james@laravel.com',
    'taylor' => 'taylor@laravel.com',
]);
```

When a key is present, Pest will use it when generating the test's description.

<div class="code-snippet">
    <img src="/assets/img/datasets-named.webp?1" style="--lines: 2" />
</div>

If the test name includes `:dataset`, the description will be interpolated into the test name at that location:

```bash
  ✓ it validates the "first_name" field
  ✓ it validates the "email" field
```

Note that when you use closures in your dataset, you must declare the argument types in the closure passed to the test function:

```php
it('can sum', function (int $a, int $b, int $result) {
    expect(sum($a, $b))->toBe($result);
})->with([
    'positive numbers' => [1, 2, 3],
    'negative numbers' => [-1, -2, -3],
    'using closure' => [fn () => 1, 2, 3],
]);
```

For larger or more complex scenarios, you may use closures:

```php

// Returning an array
test('The array contains only integers', function ($i) {
    expect($i)->toBeInt();
})->with(fn (): array => range(1, 99));

// Using a generator
test('The generator produces only integers', function ($i) {
    expect($i)->toBeInt();
})->with(function (): Generator {
    for ($i = 1 ; $i < 100_000_000_000 ; $i++) {
        yield $i;
    }
});
```

## Named Parameters

When you use datasets with associative arrays, Pest matches the dataset keys to the closure's parameter names, regardless of order. This allows you to define your dataset in any key order, and the values will be mapped to the correct parameters automatically:

```php
it('has user data', function (string $email, string $name) {
    expect($name)->toBeString();
    expect($email)->toContain('@');
})->with([
    ['name' => 'Taylor', 'email' => 'taylor@laravel.com'],
    ['name' => 'Nuno', 'email' => 'enunomaduro@gmail.com'],
]);
```

As you can see, even though the dataset defines `name` before `email`, Pest maps them correctly to the closure parameters `$email` and `$name`.

Named parameters also work with shared datasets and bound closures:

```php
dataset('users', [
    ['name' => 'Taylor', 'email' => 'taylor@laravel.com'],
    ['name' => 'Nuno', 'email' => 'enunomaduro@gmail.com'],
]);

it('has user data', function (string $email, string $name) {
    expect($name)->toBeString();
    expect($email)->toContain('@');
})->with('users');
```

## Bound Datasets

Pest's bound datasets allow you to obtain a dataset that is resolved after the `beforeEach()` method of your tests has run. This is particularly helpful in Laravel applications (or any other Pest integration) where you may need a dataset of `App\Models\User` models created after your database schema is prepared by the `beforeEach()` method:

```php
it('can generate the full name of a user', function (User $user) {
    expect($user->full_name)->toBe("{$user->first_name} {$user->last_name}");
})->with([
    fn() => User::factory()->create(['first_name' => 'Nuno', 'last_name' => 'Maduro']),
    fn() => User::factory()->create(['first_name' => 'Luke', 'last_name' => 'Downing']),
    fn() => User::factory()->create(['first_name' => 'Freek', 'last_name' => 'Van Der Herten']),
]);
```

If you wish, you may bind a single argument to the test case. However, Pest requires that it be fully typed in the `it|test` function arguments:

```diff
-it('can generate the full name of a user', function ($user, $fullName) {
+it('can generate the full name of a user', function (User $user, $fullName) {
    expect($user->full_name)->toBe($fullName);
})->with([
    [fn() => User::factory()->create(['first_name' => 'Nuno', 'last_name' => 'Maduro']), 'Nuno Maduro'],
    [fn() => User::factory()->create(['first_name' => 'Luke', 'last_name' => 'Downing']), 'Luke Downing'],
    [fn() => User::factory()->create(['first_name' => 'Freek', 'last_name' => 'Van Der Herten']), 'Freek Van Der Herten'],
]);
```

## Sharing Datasets

By storing your datasets separately in the `tests/Datasets` folder, you may keep them distinct from your test code and ensure they do not clutter your main test files:

```diff
// tests/Unit/ExampleTest.php...
it('has emails', function (string $email) {
    expect($email)->not->toBeEmpty();
-})->with(['enunomaduro@gmail.com', 'other@example.com']);
+})->with('emails');

// tests/Datasets/Emails.php...
+dataset('emails', [
+    'enunomaduro@gmail.com',
+    'other@example.com'
+]);
```

Bound datasets, description keys, and the other rules that apply to inline datasets may also be applied to shared datasets.

### Scoped Datasets

Sometimes you may have datasets that pertain only to a specific feature or set of folders. In such cases, rather than distributing the dataset globally within the `Datasets` folder, you may create a `Datasets.php` file within the folder that requires the dataset, restricting the dataset's scope to that folder alone:

```php
// tests/Feature/Products/ExampleTest.php...
it('has products', function (string $product) {
    expect($product)->not->toBeEmpty();
})->with('products');

// tests/Feature/Products/Datasets.php...
dataset('products', [
    'egg',
    'milk'
]);
```

## Combining Datasets

You may obtain complex datasets by combining both **inline** and **shared** datasets. When you do, the datasets will be combined using a [cartesian product](https://en.wikipedia.org/wiki/Cartesian_product) approach.

In the following example, we verify that each of the specified businesses is closed on every one of the provided weekdays:

```php
dataset('days_of_the_week', [
    'Saturday',
    'Sunday',
]);

test('business is closed on day', function(string $business, string $day) {
    expect(new $business)->isClosed($day)->toBeTrue();
})->with([
    Office::class,
    Bank::class,
    School::class
])->with('days_of_the_week');
```

When you run the example above, Pest's output will contain a description of each validated combination.

<div class="code-snippet">
    <img src="/assets/img/datasets-businesshours.webp?1" style="--lines: 10" />
</div>

## Describe Blocks With Datasets

You may attach a dataset to a `describe()` block, and every test within that block will receive the dataset values:

```php
describe('user notifications', function () {
    test('can send notification', function (string $channel) {
        expect($channel)->toBeString();
    });

    test('can queue notification', function (string $channel) {
        expect($channel)->toBeIn(['mail', 'sms']);
    });
})->with(['mail', 'sms']);
```

You may also use `beforeEach()->with()` inside a `describe()` block to apply a dataset to all tests within that scope:

```php
describe('user settings', function () {
    beforeEach()->with([10, 20, 30]);

    test('receives the dataset value', function (int $value) {
        expect($value)->toBeGreaterThan(0);
    });
});
```

## Repeating Tests

Sometimes you may need to repeat a test multiple times, whether for debugging purposes or to ensure that it is stable. On these occasions, you may use the `repeat()` method to run a test a given number of times:

```php
it('can repeat a test', function () {
    $result = /** Some code that may be unstable */;

    expect($result)->toBeTrue();
})->repeat(100); // Repeat the test 100 times
```

---

Now that you are comfortable using datasets in your tests, the next step is to learn how to test for exceptions, verifying that your code behaves correctly and throws the appropriate exceptions when it encounters unexpected or erroneous input: [Exceptions →](/docs/exceptions)
