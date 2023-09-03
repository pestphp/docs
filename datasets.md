---
title: Datasets
description: With datasets, you can define an array of test data and Pest will run the same test for each set automatically. This saves time and effort by eliminating the need to repeat the same test manually with different data.
---

# Datasets

With datasets, you can define an array of test data and Pest will run the same test for each set automatically. This saves time and effort by eliminating the need to repeat the same test manually with different data.

```php
it('has emails', function (string $email) {
    expect($email)->not->toBeEmpty();
})->with(['enunomaduro@gmail.com', 'other@example.com']);
```

When running your tests, Pest will automatically add informative test descriptions to tests that use datasets, outlining the parameters used in each test, aiding in understanding the data and identifying issues if a test fails.

<div class="code-snippet">
    <img src="/assets/img/datasets-emails.webp?1" style="--lines: 3" />
</div>

Naturally, it is possible to supply multiple arguments by providing an array containing arrays of arguments.

```php
it('has emails', function (string $name, string $email) {
    expect($email)->not->toBeEmpty();
})->with([
    ['Nuno', 'enunomaduro@gmail.com'],
    ['Other', 'other@example.com']
]);
```

To manually add your own description to a dataset value, you may simply assign it a key.

```php
it('has emails', function (string $email) {
    expect($email)->not->toBeEmpty();
})->with([
    'james' => 'james@laravel.com',
    'taylor' => 'taylor@laravel.com',
]);
```

If a key is added, Pest will use the key when generating the description for the test.

<div class="code-snippet">
    <img src="/assets/img/datasets-named.webp?1" style="--lines: 2" />
</div>

It is important to notice that when using `closures` in your dataset, you must declare the arguments type in the closure passed to the test function.

```php
it('can sum', function (int $a, int $b, int $result) {
    expect(sum($a, $b))->toBe($result);
})->with([
    'positive numbers' => [1, 2, 3],
    'negative numbers' => [-1, -2, -3],
    'using closure' => [fn () => 1, 2, 3],
]);
```

## Bound Datasets

Pest's bound datasets can be used to obtain a dataset that is resolved after the `beforeEach()` method of your tests. This is particularly useful in Laravel applications (or any other Pest integration) where you may need a dataset of `App\Models\User` models that are created after your database schema is prepared by the `beforeEach()` method.

```php
it('can generate the full name of a user', function (User $user) {
    expect($user->full_name)->toBe("{$user->first_name} {$user->last_name}");
})->with([
    fn() => User::factory()->create(['first_name' => 'Nuno', 'last_name' => 'Maduro']),
    fn() => User::factory()->create(['first_name' => 'Luke', 'last_name' => 'Downing']),
    fn() => User::factory()->create(['first_name' => 'Freek', 'last_name' => 'Van Der Herten']),
]);
```

If you want, you can bind a single argument to the test case. However, Pest requires that it must be fully typed in the `it|test` function arguments.

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

By storing your datasets separately in the `tests/Datasets` folder, you can easily distinguish them from your test code and ensure that they do not clutter your main test files.

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

Bound datasets, description keys, and other rules that are applicable to inline datasets can also be applied to shared datasets.

### Scoped Datasets

Occasionally, datasets may pertain only to a specific feature or set of folders. In such cases, rather than distributing the dataset globally within the `Datasets` folder, you can generate a `Datasets.php` file within the relevant folder requiring the dataset and restrict the dataset's scope to that folder alone.

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

You can easily obtain complex datasets by combining both **inline** and **shared** datasets. When doing so, the datasets will be combined using a [cartesian product](https://en.wikipedia.org/wiki/Cartesian_product) approach.

In the following example, we verify that all of the specified businesses are closed on each of the provided weekdays.

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

When running the example above, Pest's output will contain a description of each of the validated combinations.

<div class="code-snippet">
    <img src="/assets/img/datasets-businesshours.webp?1" style="--lines: 10" />
</div>

## Repeating Tests

In some cases, you may need to repeat a test multiple times for debugging purposes or to ensure that the test is stable. On these occasions, you may use the `repeat()` method to repeat a test a given number of times.

```php
it('can repeat a test', function () {
    $result = /** Some code that may be unstable */;

    expect($result)->toBeTrue();
})->repeat(100); // Repeat the test 100 times
```

---

After becoming skilled at utilizing datasets for testing, the next crucial step is to gain an understanding of how to test for exceptions. This involves verifying that your code behaves correctly and throws appropriate exceptions when it encounters unexpected or erroneous input: [Exceptions →](/docs/exceptions)
