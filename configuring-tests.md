---
title: Configuring Tests
description: The `Pest.php` file is a configuration file that is used to define your test suite setup. This file is located in the `tests` directory of your project and is automatically loaded by Pest when you run your tests. Although you can define Global Hooks or Custom Expectations within this file, its primary purpose is to specify the base test class utilized in your test suite.
---

# Configuring Tests

The `Pest.php` file is a configuration file used to define your test suite setup. This file is located in the `tests` directory of your project and is automatically loaded by Pest when you run your tests. Although you may define [Global Hooks](/docs/global-hooks) or [Custom Expectations](/docs/custom-expectations) within this file, its primary purpose is to specify the base test class used across your test suite.

When using Pest, the `$this` variable available within the closures you provide to test functions is bound to a specific test case class, which is typically `PHPUnit\Framework\TestCase`. This ensures that test cases written in Pest's functional style may access the underlying assertion API of PHPUnit, simplifying collaboration with other developers who are more familiar with the PHPUnit testing framework.

```php
it('has home', function () {
    echo get_class($this); // \PHPUnit\Framework\TestCase

    $this->assertTrue(true);
});
```

However, you may associate a specific folder, or even your entire test suite, with another base test case class, thereby changing the value of `$this` within your tests. To accomplish this, you may use the `pest()` function together with the `in()` method within your `Pest.php` configuration file:

```php
// tests/Pest.php
pest()->extend(Tests\TestCase::class)->in('Feature');

// tests/Feature/ExampleTest.php
it('has home', function () {
    echo get_class($this); // \Tests\TestCase
});
```

In addition, Pest supports [glob patterns](https://www.php.net/manual/en/function.glob.php) in the `in()` method, allowing you to specify multiple directories or files with a single pattern. Glob patterns are string representations that match various file paths, much like wildcards. If you are unfamiliar with glob patterns, refer to the PHP manual [here](https://www.php.net/manual/en/function.glob.php):

```php
// tests/Pest.php
pest()->extend(Tests\TestCase::class)->in('Feature/*Job*.php');

// This will apply the Tests\TestCase to all test files in the "Feature" directory that contains "Job" in their filename.
```

For a more complex example, you may use a pattern to match multiple directories across different modules while applying multiple test case classes and traits:

```php
// tests/Pest.php
pest()
    ->extend(DuskTestCase::class)
    ->use(DatabaseMigrations::class)
    ->in('../Modules/*/Tests/Browser');

// This will apply the DuskTestCase class and the DatabaseMigrations trait to all test files within any module's "Browser" directory.
```

Any method defined as `public` or `protected` in your base test case class may be accessed within the test closure:

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
pest()->extend(TestCase::class)->in('Feature');

// tests/Feature/ExampleTest.php
it('has home', function () {
    $this->performThis();
});
```

A trait may be linked to a test or folder, much like a class. For instance, in Laravel, you may use the `RefreshDatabase` trait to reset the database prior to each test. To include the trait in your test, pass the trait's name to the `pest()->use()` method:

```php
<?php

use Tests\TestCase;
use Illuminate\Foundation\Testing\RefreshDatabase;

pest()->extend(TestCase::class)->use(RefreshDatabase::class)->in('Feature');
```

To associate a particular test with a specific test case class or trait, you may use the `pest()->extend()` and `pest()->use()` methods **within that specific test file**, omitting the `in()` method:

```php
pest()->extend(Tests\MySpecificTestCase::class);

it('has home', function () {
    echo get_class($this); // \Tests\MySpecificTestCase
});
```

---

Next, one of the features available to you when configuring your test suite is the ability to group folders. Once in place, this feature allows you to filter the tests you execute using the `--group` option: [Grouping Tests](/docs/grouping-tests)
