---
title: Grouping Tests
description: You can assign tests folders to various groups using Pest's `group()` method. Assigning a group to a set of relatively slow tests could be beneficial since it allows you to selectively execute them separately from the rest of your test suite. Typically, the process of assigning a set of tests to a group is done within your `Pest.php` configuration file.
---

# Grouping Tests

You can assign tests folders to various groups using Pest's `group()` method. Assigning a group to a set of relatively slow tests could be beneficial since it allows you to selectively execute them separately from the rest of your test suite. Typically, the process of assigning a set of tests to a group is done within your `Pest.php` configuration file.

For instance, consider the scenario where we assign the tests located in the `tests/Feature` folder to a group named "feature".

```php
uses(TestCase::class)
    ->group('feature')
    ->in('Feature');
```

As previously stated in the [Filtering Tests](/docs/filtering-tests) documentation, you can use the `--group` option to execute tests belonging to a specific group.

```bash
./vendor/bin/pest --group=feature
```

You also have the option to assign a particular test to a group by chaining the `group()` method onto the test function.

```php
it('has home', function () {
    //
})->group('feature');
```

You may also assign a test to multiple groups.

```php
it('has home', function () {
    //
})->group('feature', 'browser');
```

In some cases, you may want to assign a whole file to a group. To do so, you may combine the `uses()` and `group()` methods.

```php
uses()->group('feature');

it('has home', function () {
    //
});
```

---

When you are setting up a test suite, it may be necessary to share common hooks between different folders and groups. In such cases, Global Hooks can prove to be helpful: [Global Hooks](/docs/global-hooks)
