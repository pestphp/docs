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

Next section: [Coverage →](/docs/coverage)
