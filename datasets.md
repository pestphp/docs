---
title: Datasets
description: With datasets, you can define an array of test data and Pest will run the same test for each set automatically. This saves time and effort by eliminating the need to repeat the same test manually with different data.
---

# Datasets

With datasets, you can define an array of test data and Pest will run the same test for each set automatically. This saves time and effort by eliminating the need to repeat the same test manually with different data.

```php
it('has emails', function ($email) {
    expect($email)->not->toBeEmpty();
})->with(['enunomaduro@gmail.com', 'other@example.com']);
```

When running your tests, Pest will automatically add informative test descriptions to tests that use datasets, outlining the parameters used in each test, aiding in understanding the data and identifying issues if a test fails.

<div class="code-snippet">
    <img src="/assets/img/datasets-emails.webp?1" style="--lines: 3" />
</div>

Naturally, it is possible to supply multiple arguments by providing an array containing arrays of arguments.

```php
it('has emails', function ($name, $email) {
    expect($email)->not->toBeEmpty();
})->with([
    ['Nuno', 'enunomaduro@gmail.com'],
    ['Other', 'other@example.com']
]);
```

To manually add your own description to a dataset value, you may simply assign it a key.

```php
it('has emails', function ($email) {
    expect($email)->not->toBeEmpty();
})->with([
    'james' => 'james@laravel.com',
    'taylor' => 'taylor@laravel.com',
]);
```

If a key is added, Pest will use the key when generating the description for the test.

```php
✓ it has emails with data set "james"
✓ it has emails with data set "taylor"
```


## Bound Datasets

Pest's bound datasets can be used to obtain a dataset that is resolved after the `beforeEach()` method of your tests. This is particularly useful in Laravel applications (or any other Pest integration) where you may need a dataset of `App\Models\User` models that are created after your database schema is prepared by the `beforeEach()` method.

```php
it('can calculate the full name of a user', function (User $user) {
    expect($user->full_name)->toBe("{$user->first_name} {$user->last_name}");
})->with([
    fn() => User::factory()->create(['first_name' => 'Nuno', 'last_name' => 'Maduro']),
    fn() => User::factory()->create(['first_name' => 'Luke', 'last_name' => 'Downing']),
    fn() => User::factory()->create(['first_name' => 'Freek', 'last_name' => 'Van Der Herten']),
]);
```

## Sharing Datasets

By storing your datasets separately in the `tests/Datasets` folder, you can easily distinguish them from your test code and ensure that they do not clutter your main test files.

```diff
// tests/Unit/ExampleTest.php...
it('has emails', function ($email) {
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
it('has products', function ($product) {
    expect($product)->not->toBeEmpty();
})->with('products');

// tests/Feature/Products/Datasets.php...
dataset('products', [
    'beer',
    'wine'
]);
```

## Combining Datasets

You can easily obtain complex datasets by combining both **inline** and **shared** datasets. When doing so, the datasets will be combined using a [cartesian product](https://en.wikipedia.org/wiki/Cartesian_product) approach.

```php
dataset('days_of_the_week', [
    'Monday',
    'Tuesday',
    //
]);

test('business hours', function($business, $day) {
    expect($business)->isOpen($day)->toBeTrue();
})->with([
    Bar::class,
    Restaurant::class,
])->with('days_of_the_week');
```

---

After becoming skilled at utilizing datasets for testing, the next crucial step is to gain an understanding of how to test for exceptions. This involves verifying that your code behaves correctly and throws appropriate exceptions when it encounters unexpected or erroneous input: [Exceptions →](/docs/exceptions)
