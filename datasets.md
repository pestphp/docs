---
title: Datasets
description: Datasets
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

Bound datasets in Pest can be used to obtain a dataset that is resolved after the `beforeEach method` of your tests, which can be quite helpful. This is particularly useful in Laravel applications where you may need a dataset of User models that have already been persisted to the database.

```php
it('can calculate the full name of a user', function(User $user) {
    expect($user->full_name)->toBe("{$user->first_name} {$user->last_name}");
})->with([
    fn() => User::factory()->create(['first_name' => 'Nuno', 'last_name' => 'Maduro']),
    fn() => User::factory()->create(['first_name' => 'Luke', 'last_name' => 'Downing']),
    fn() => User::factory()->create(['first_name' => 'Freek', 'last_name' => 'Van Der Herten']),
]);
```

---

Next section: [Coverage →](/docs/coverage)



### Shared Datasets

If you want to keep your test files clean, you can use
the `tests/Datasets` folder to place your datasets:
```php
tests
    - Unit/ComponentTest.php
    - Feature/HomeTest.php
    - Datasets/Emails.php <--
phpunit.xml
```

Inside this `Emails.php` you just have to use the `dataset()` function:
```php
<?php

dataset('emails', [
    'enunomaduro@gmail.com',
    'other@example.com'
]);
```

In your test, you can use the `dataset` name to reuse your dataset:
```php
it('has emails', function ($email) {
    expect($email)->not->toBeEmpty();
})->with('emails');
```
---

<a name="lazy-datasets"></a>
### Lazy Datasets

In both **inline** and **external** approaches, you may need to serve your
data with PHP's generators to allow you to work with very large datasets
while keeping memory usage low:

```php
dataset('emails', function () {
    yield 'enunomaduro@gmail.com';
    yield 'other@example.com';
});

it('has emails', function ($email) {
    expect($email)->not->toBeEmpty();
})->with(function () {
    yield 'enunomaduro@gmail.com';
    yield 'other@example.com';
});
```

---

<a name="bound-datasets"></a>
### Bound Datasets

Sometimes, it's helpful to have a dataset that is resolved *after* the `beforeEach` method of your tests. For example,
in Laravel applications, you might want a dataset of `User` models that have been persisted to the database.

Pest allows you to do this using bound datasets:

```php
it('can calculate the full name of a user', function(User $user) {
    expect($user->full_name)->toBe("{$user->first_name} {$user->last_name}");
})->with([
    fn() => User::factory()->create(['first_name' => 'Nuno', 'last_name' => 'Maduro']),
    fn() => User::factory()->create(['first_name' => 'Luke', 'last_name' => 'Downing']),
    fn() => User::factory()->create(['first_name' => 'Freek', 'last_name' => 'Van Der Herten']),
]);
```

> **Note:** You can access the TestCase using `$this` inside of the closure.

Shared datasets may also have bound access:

```php
dataset('users', function () {
    yield fn() => User::factory()->blocked()->create();
    yield fn() => User::factory()->unverified()->create();
    yield fn() => User::factory()->inactive()->create();
});
```

---

<a name="combining-datasets"></a>
### Combining datasets

Both, **inline** and **external**, datasets can be combined to generate a new dataset with a [cartesian product](https://en.wikipedia.org/wiki/Cartesian_product) approach. This means that you can obtain complex datasets with a simple approach:

```php
dataset('days_of_the_week', [
    'Monday',
    'Tuesday',
    // ...
]);

test('business hours', function($business, $day) {
    expect($business)->isOpen($day)->toBeTrue();
})->with([
    Bar::class,  
    Restaurant::class,
])->with('days_of_the_week');
```

<a name="datasets-description"></a>
### Datasets description

By default, when running a test with a dataset, Pest generates a small description of the dataset based on the dataset values:

```php
✓ it has emails with (james@laravel.com)
✓ it has emails with (taylor@laravel.com)
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

When adding a `key`, the `key` value will be used instead:

```php
✓ it has emails with data set "james"
✓ it has emails with data set "taylor"
```

---

Next section: [Coverage →](/docs/coverage)
