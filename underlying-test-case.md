---
title: Underlying Test Case
description: Underlying Test Case
---

# Underlying Test Case

- [Overview](#overview)

<a name="overview"></a>

## Overview

The closure you provide to your test function is always bound to a specific
test case class. By default, that class is `PHPUnit\Framework\TestCase`:

```php
<?php

it('has home', function () {
    $this->assertTrue(true);

    // \PHPUnit\Framework\TestCase
    echo get_class($this);
});
```

In real-world applications, you may have to change it, and for that, you
can call the `uses` function:

### `uses()`

The `uses` function binds a class and/or trait to your test files.
This is how you would bind a trait to your current file:

```php
<?php

use Illuminate\Foundation\Testing\RefreshDatabase;

// Uses the given trait in the current file
uses(RefreshDatabase::class);
```

The `in()` function lets you use the given class and/or trait recursively inside the specified folder:

```php
<?php

use Tests\TestCase;

// Uses the given test case in the "Feature" folder recursively
uses(TestCase::class)->in('Feature');
```

You can use multiple `uses()` calls if they target **different folders**:

```php
<?php

use Tests\TestCase;
use Illuminate\Foundation\Testing\RefreshDatabase;

// Uses the given test case in the "Feature" folder recursively
uses(TestCase::class)->in('Feature');

// Uses the given trait in the "Unit" folder recursively
uses(RefreshDatabase::class)->in('Unit');
```

To bind multiple classes and/or traits at once, group them in your `uses()` call like so:

```php
<?php

use Tests\TestCase;
use Illuminate\Foundation\Testing\RefreshDatabase;

// Uses the given test case and trait in the current folder recursively
uses(TestCase::class, RefreshDatabase::class)->in(__DIR__);
```

To override a property on the underlying testcase you can use the `with()` method on the result of a `uses()` call, and supply it with
the name of the property you're extending, and a valid value for a property. You can also supply an array of property/value pairs to
override multiple properties at once:

```php
<?php

use App\Models\DatabaseMode;
use Tests\TestCase;

uses(TestCase::class)
    ->with('appName', 'MyAwesomePestApp')
    ->with([
        'useDatabase'   => true,
        'databaseMode'  => DatabaseMode::TRANSACTION,
    ]);
```

For extending or overriding a method of the underlying testcase, call the `extends()` method on the result of a `uses()` call,
and supply the method name and the new method expressed as an anonymous function. You can also supply an array of method/value
sets for overriding multiple methods or chain extends call.

```php
<?php

use App\Models\Role;
use Tests\TestCase;

uses(TestCase::class)
    ->extends('createBill', function (string $name) {
        $this->billService->create($name);
    })
    ->extends([
        'getDefaultRole' => fn () => Role::USER,
        'getSpecialRole' => fn () => Role::ADMIN,
    ]);

```

The scope of the function is bound to the underlying testcase while running the test, so you're free to use `$this`, or call the original method
by using `parent::methodName()`. Calling the parent method from static methods is currently not supported due to limitations within the PHP language itself.

## `tests/Pest.php`

Please consider creating a file with the name `tests/Pest.php` on the
root directory of your tests folder. No worries, that file
is autoloaded automatically.
It's an ideal place to recursively bind traits and classes to your tests with the `uses()` function.

```
tests
    - Unit/ComponentTest.php
    - Feature/HomeTest.php
    - Pest.php <--
phpunit.xml
```

Next section: [Assertions →](/docs/assertions)
