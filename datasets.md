---
title: Datasets
description: With datasets, you can define an array of test data, and Pest will run the same test for each set automatically. This saves time and effort by eliminating the need to repeat the same test manually with different data.
---

# Datasets

With datasets, you can define an array of test data, and Pest will run the same test for each set automatically. This saves time and effort by eliminating the need to repeat the same test manually with different data.

```php
it('has emails', function ($email) {
    expect($email)->not->toBeEmpty();
})->with(['enunomaduro@gmail.com', 'other@example.com']);
```

The test descriptions in Pest datasets are informative, outlining the parameters used in each test, aiding in understanding the data and identifying issues if a test fails.

```php
✓ it has emails with (enunomaduro@gmail.com)
✓ it has emails with (other@example.com)
```

Naturally, it is possible to supply multiple arguments by presenting an array of arguments.

```php
it('has emails', function ($name, $email) {
    expect($email)->not->toBeEmpty();
})->with([
    ['Nuno', 'enunomaduro@gmail.com'],
    ['Other', 'other@example.com']
]);
```

To add a description to the dataset values, you can specify a key yourself.

```php
it('has emails', function ($email) {
    expect($email)->not->toBeEmpty();
})->with([
    'james' => 'james@laravel.com',
    'taylor' => 'taylor@laravel.com',
]);
```

If a key is added, its corresponding key value will be utilized in place of the original value for the description.

```php
✓ it has emails with data set "james"
✓ it has emails with data set "taylor"
```


## Bound datasets

Bound datasets in Pest can be used to obtain a dataset that is resolved after the `beforeEach` method of your tests, which can be quite helpful. This is particularly useful in Laravel applications (or any other Pest integration) where you may need a dataset of `App\Models\User` models that have already been persisted to the database.

```php
it('can calculate the full name of a user', function(User $user) {
    expect($user->full_name)->toBe("{$user->first_name} {$user->last_name}");
})->with([
    fn() => User::factory()->create(['first_name' => 'Nuno', 'last_name' => 'Maduro']),
    fn() => User::factory()->create(['first_name' => 'Luke', 'last_name' => 'Downing']),
    fn() => User::factory()->create(['first_name' => 'Freek', 'last_name' => 'Van Der Herten']),
]);
```

## Sharing Datasets

By storing your datasets separately in the `tests/Datasets` folder, you can easily distinguish them from your test code and ensure that they do not clutter up your main test files.

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

Bound datasets, description keys, and other rules that are applicable to inline datasets can also be applied to shared datasets. Thus, these features can be used to manipulate shared datasets as well.

### Scope Datasets

Occasionally, datasets may pertain only to a specific feature or set of folders. In such cases, rather than distributing the dataset globally within the Datasets folder, you can generate a `Datasets.php` file within the relevant folder requiring the dataset and restrict the dataset's scope to that folder alone.

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

You can easily obtain complex datasets by combining both **inline** and **shared** datasets using a [cartesian product](https://en.wikipedia.org/wiki/Cartesian_product) approach. This allows for a straightforward method to generate new datasets.

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

After mastering the use of datasets for testing, the next step is to explore "Test Filtering". This feature allows you to efficiently run specific tests based on criteria like test name, dirty files, etc: [Filtering Tests →](/docs/filtering-tests)
