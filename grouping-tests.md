---
title: Grouping Tests
description: You can assign tests to various groups using Pest's group method, which is optional. Assigning a group to a set of relatively slow tests could be beneficial, allowing you to selectively execute them together.
---

# Grouping Tests

You can assign tests folders to various groups using Pest's `group` method, which is optional. Assigning a group to a set of relatively slow tests could be beneficial, allowing you to selectively execute them together. In general, the process of assigning a set of tests to a group is done through your `Pest.php` configuration file.

For instance, consider the scenario where we assign the tests located in the `tests/Feature` folder to a particular group named "feature".

```php
uses(TestCase::class)
    ->group('feature')
    ->in('Feature');
```

As previously stated in the [Filtering Tests](/docs/filtering-tests) documentation, you can use the `--group` option to execute tests belonging to a specific group.

```bash
./vendor/bin/pest --group=feature
```

You also have the option to assign a particular test to a specific group by chaining the group method onto an it or test function, if desired.

```php
it('has home', function () {
    //
})->group('feature');
```

Naturally, you can assign a test to multiple groups as well.

```php
it('has home', function () {
    //
})->group('feature', 'browser');
```

In some cases, you may want to assign a whole file to a group. Keep in mind that to do so, you should use the `uses()` function without the `in()` method on the specific test file.

```php
uses()->group('feature');

it('has home', function () {
    //
});
```

---

When you are setting up a test suite, it may be necessary to share common hooks between different folders and groups. In such cases, Global Hooks can prove to be helpful: [Global Hooks](/docs/global-hooks)
