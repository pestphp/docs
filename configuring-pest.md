---
title: Configuring Pest
description: The `Pest.php` file is a configuration file that is used to define your test suite setup.
---

# Configuring Pest

The `Pest.php` file is a configuration file that is used to define your test suite setup. This file is located in the `tests` directory of your project and is automatically loaded by Pest when you run your tests. Although you can define [Global Hooks](/docs/global-hooks) or [Custom Expectations](/docs/custom-expectations) within this file, its primary purpose is to specify the base test class utilized in your test suite.

In Pest, the closure supplied to your test function is, by default, linked with a specific test case class, which is typically `PHPUnit\Framework\TestCase`. The default configuration guarantees that the test cases written in Pest's functional style can access the assertion API, simplifying collaboration with other developers who are more conversant with the PHPUnit testing framework.

```php
it('has home', function () {
    echo get_class($this); // \PHPUnit\Framework\TestCase

    $this->assertTrue(true);
});
```

Usually, users prefer associating a specific folder or the whole test suite with a particular test case class for convenience. If you're in this situation, you can utilize the `Pest.php` configuration file and the combined functions of `uses()` and `in()`.

```php
// tests/Pest.php
uses(Tests\TestCase::class)->in('Feature');

// tests/Feature/ExampleTest.php
it('has home', function () {
    echo get_class($this); // \Tests\TestCase
});
```

Certainly, any method that is defined as public or protected at the test case level can be accessed within the test closure.

```php
use PHPUnit\Framework\TestCase as BaseTestCase;

// tests/TestCase.php
class TestCase extends BaseTestCase
{
    public function performThis(): void
    {
        // ...
    }
}

// tests/Pest.php
uses(TestCase::class)->in('Feature');

// tests/Feature/ExampleTest.php
it('has home', function () {
    $this->performThis();
});
```

A trait can be linked to a test or folder, much like classes. For instance, in Laravel, you can employ the `RefreshDatabase` trait to reset the database prior to each test. To include the trait in your test, pass it through the `uses()` function.

```php
<?php

use Tests\TestCase;
use Illuminate\Foundation\Testing\RefreshDatabase;

uses(TestCase::class, RefreshDatabase::class)->in('Feature');
```

To associate a particular test with a specific test case class or trait, you can utilize the `uses()` function **on the test file directly**, omitting the use of the `in()` method.

```php
uses(Tests\MySpecificTestCase::class);

it('has home', function () {
    echo get_class($this); // \Tests\MySpecificTestCase
});
```

---

Next, one of the features available to you when setting up your test suite is the ability to group folders, in addition to configuring the base test case class. This feature allows you to filter your test executions by group, using the --group option, at a later point in time: [Grouping Tests](/docs/grouping-tests)

