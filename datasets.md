---
title: Datasets
description: Datasets
---

# Datasets

- [Overview](#overview)
- [Create Datasets](#create-datasets)
    - [Inline Datasets](#inline-datasets)
    - [Shared Datasets](#shared-datasets)
    - [Lazy Datasets](#lazy-datasets)
    - [Bound Datasets](#bound-datasets)
    - [Combining Datasets](#combining-datasets)
    - [Datasets description](#datasets-description)

<a name="overview"></a>
## Overview

Datasets in Pest allows you to run the same test multiple times with
different data. Also, the test description will contain information
about the arguments used on each test.

> This feature is most commonly known as Data Providers in PHPUnit.

<a name="create-datasets"></a>
## Create Datasets

Now, in this section, we are going to cover the way you can create
datasets in Pest.

<a name="inline-datasets"></a>
### Inline Datasets

An inline `dataset` may be used for a single test only:
```php
it('has emails', function ($email) {
    expect($email)->not->toBeEmpty();
})->with([
    'enunomaduro@gmail.com',
    'other@example.com'
]);
```

Of course, you can also give multiple
arguments providing an array of arguments:
```php
it('has emails', function ($name, $email) {
    expect($email)->not->toBeEmpty();
})->with([
    ['Nuno', 'enunomaduro@gmail.com'],
    ['Other', 'other@example.com']
]);
```
---

<a name="shared-datasets"></a>
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
✓ has emails with (james@laravel.com)
✓ has emails with (taylor@laravel.com)
```

You may specify yourself the dataset description adding a `key` to the dataset values:

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
✓ has emails with (james)
✓ has emails with (taylor)
```

---

Next section: [Coverage →](/docs/coverage)
