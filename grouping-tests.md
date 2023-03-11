---
title: Grouping Tests
description: You can assign tests to various groups using Pest's group method, which is optional. Assigning a group to a set of relatively slow tests could be beneficial, allowing you to selectively execute them together.
---

# Grouping Tests

You can assign tests folders to various groups using Pest's `group` method, which is optional. Assigning a group to a set of relatively slow tests could be beneficial, allowing you to selectively execute them together.

As example, where we are assigning the `tests/Integration` folder to a specific group called "integration".

```php
uses(TestCase::class)
    ->group('integration')
    ->in('Integration');
```

As previously stated in the [Filtering Tests](/docs/filtering-tests) documentation, you can use the `--group` option to execute tests belonging to a specific group.

```bash
./vendor/bin/pest --group=integration
```

The expected behavior when filtering tests by a specific group is that the output will only display the tests belonging to that group.

```php
✓ it has emails with (enunomaduro@gmail.com)
```

You have the option to assign a particular test to a specific group by chaining the group method onto an it or test function, if desired.

```php
it('has home', function () {
    //
})->group('integration');
```

Naturally, you can assign a test to multiple groups as well.

```php
it('has home', function () {
    // ..
})->group('integration', 'browser');
```

In some cases, you may want to assign a whole file to a group. Keep in mind that to do so, you should use the `uses()` function without the `in()` method on the specific test file.

```php
uses()->group('integration');

it('has home', function () {
    //
});
```

---

Next, one of the features available to you when setting up your test suite is the ability to group folders, in addition to configuring the base test case class. This feature allows you to filter your test executions by group, using the --group option, at a later point in time: [Grouping Tests](/docs/grouping-tests)
