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
    - [Combining Multiple Datasets](#matrix-datasets)

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

<a name="matrix-datasets"></a>
### Combining multiple datasets

Multiple (both **inline** and **external**) datasets can be combined in order to generate a new dataset with a [cartesian product](https://en.wikipedia.org/wiki/Cartesian_product) approach. This means that you can obtain complex datasets with a simple approach:

```php
dataset('days_of_week', [
    'Sunday',
    'Monday',
    'Tuesday',
    'Wednesday',
    'Thursday',
    'Friday',
    'Saturday',
]);

it('can generate awesome code', function($developer, $dayOfWeek){
    expect(new Developer($develoepr)->codeOn($dayOfWeek))->toBeAwesome();
})->with([
    'Nuno',  
    'Owen',  
    'Oliver',  
    'Fabio',  
])->with('days_of_week');
```

this example wil result in executing the test with the following dataset:

```
[
  ['Nuno', 'Sunday'],
  ['Nuno', 'Monday'],
  ['Nuno', 'Tuesday'],
  
   ...
  
  ['Owen', 'Tuesday'],
  ['Owen', 'Monday'],
  ['Owen', 'Tuesday'],
  
   ...
  
  ['Oliver', 'Tuesday'],
  ['Oliver', 'Monday'],
  ['Oliver', 'Tuesday'],
  
   ...and so on
]
```

a more readable approach to combine multiple datasets (usually with small or named datasets) is to puth them in the same `with` method:

```php
it('can generate awesome code', function($developer, $dayOfWeek){
    expect(new Developer($develoepr)->codeOn($dayOfWeek))->toBeAwesome();
})->with('developers', 'days_of_week');
```

**bonus:** you are not limited to only two datasets, byt can combine any number of them 

```php
it('can generate awesome code, at any time, everywhere', function($developer, $dayOfWeek){
    expect(new Developer($develoepr)->codeOn($dayOfWeek))->toBeAwesome();
})->with('developers', 'days_of_week', 'hours_of_day', 'places');
```
---

Next section: [Coverage →](/docs/coverage)
