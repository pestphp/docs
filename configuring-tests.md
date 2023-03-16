---
title: Configuring Tests
description: The `Pest.php` file is a configuration file that is used to define your test suite setup. This file is located in the `tests` directory of your project and is automatically loaded by Pest when you run your tests. Although you can define Global Hooks or Custom Expectations within this file, its primary purpose is to specify the base test class utilized in your test suite.
---

# Configuring Tests

The `Pest.php` file is a configuration file that is used to define your test suite setup. This file is located in the `tests` directory of your project and is automatically loaded by Pest when you run your tests. Although you can define [Global Hooks](/docs/global-hooks) or [Custom Expectations](/docs/custom-expectations) within this file, its primary purpose is to specify the base test class utilized in your test suite.

When using Pest, the `$this` variable available within closures provided to test functions is bound to a specific test case class, which is typically `PHPUnit\Framework\TestCase`. This guarantees that the test cases written in Pest's functional style can access the underlying assertion API of PHPUnit, simplifying collaboration with other developers who are more familiar with the PHPUnit testing framework.

```php
it('has home', function () {
    echo get_class($this); // \PHPUnit\Framework\TestCase

    $this->assertTrue(true);
});
```

However, you may associate a specific folder or even your entire test suite with another base test case class, thus changing the value of `$this` within tests. To accomplish this, you can utilize the `uses()` and `in()` functions within your `Pest.php` configuration file.

```php
// tests/Pest.php
uses(Tests\TestCase::class)->in('Feature');

// tests/Feature/ExampleTest.php
it('has home', function () {
    echo get_class($this); // \Tests\TestCase
});
```

Any method that is defined as `public` or `protected` in your base test case class can be accessed within the test closure.

```php
use PHPUnit\Framework\TestCase as BaseTestCase;

// tests/TestCase.php
class TestCase extends BaseTestCase
{
    public function performThis(): void
    {
        //
    }
}

// tests/Pest.php
uses(TestCase::class)->in('Feature');

// tests/Feature/ExampleTest.php
it('has home', function () {
    $this->performThis();
});
```

A trait can be linked to a test or folder, much like classes. For instance, in Laravel, you can employ the `RefreshDatabase` trait to reset the database prior to each test. To include the trait in your test, pass the trait's name to the `uses()` function.

```php
<?php

use Tests\TestCase;
use Illuminate\Foundation\Testing\RefreshDatabase;

uses(TestCase::class, RefreshDatabase::class)->in('Feature');
```

To associate a particular test with a specific test case class or trait, you can utilize the `uses()` function **within that specific test file**, omitting the use of the `in()` method.

```php
uses(Tests\MySpecificTestCase::class);

it('has home', function () {
    echo get_class($this); // \Tests\MySpecificTestCase
});
```

---

Next, one of the features available to you when configuring your test suite is the ability to group folders. When utilized, this feature allows you to filter your executed tests using the `--group` option: [Grouping Tests](/docs/grouping-tests)
