---
title: Grouping Tests
description: Assign test folders to named groups with Pest's group() method so you may run a set of related or slow tests on their own, separately from the rest of your suite.
---

# Grouping Tests

You may assign test folders to various groups using Pest's `group()` method. Assigning a group to a set of relatively slow tests can be helpful, as it allows you to run them separately from the rest of your test suite. Typically, you should assign a set of tests to a group within your `Pest.php` configuration file.

For instance, consider a scenario where we assign the tests located in the `tests/Feature` folder to a group named "feature":

```php
pest()->extend(TestCase::class)
    ->group('feature')
    ->in('Feature');
```

As mentioned in the [Filtering Tests](/docs/filtering-tests) documentation, you may use the `--group` option to run the tests belonging to a specific group:

```bash
./vendor/bin/pest --group=feature
```

You may also assign a particular test to a group by chaining the `group()` method onto the test function:

```php
it('has home', function () {
    //
})->group('feature');
```

Of course, you may also assign a test to multiple groups:

```php
it('has home', function () {
    //
})->group('feature', 'browser');
```

If you wish to assign a group to a describe block, you may do so by chaining the `group()` method onto the describe function:

```php
describe('home', function () {
    test('main page', function () {
        //
    });
})->group('feature');
```

Sometimes you may wish to assign a whole file to a group. To accomplish this, you may use the `pest()->group()` method within the file:

```php
pest()->group('feature');

it('has home', function () {
    //
});
```

---

When setting up a test suite, you may need to share common hooks between different folders and groups. In such cases, Global Hooks can prove helpful: [Global Hooks](/docs/global-hooks)
