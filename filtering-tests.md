---
title: Filtering Tests
description: When you run `./vendor/bin/pest`, Pest executes the complete test suite by default. As you might expect, running individual tests is accomplished by passing the test name as the first argument.
---

# Test Filtering

When you run `./vendor/bin/pest`, Pest executes the complete test suite by default. As you might expect, running individual tests is accomplished by passing the test name as the first argument.

```bash
./vendor/bin/pest tests/Unit/TestExample.php
```

This chapter will cover even more ways to filter which tests are executed by Pest. For the complete CLI API Reference, please refer to our [CLI API Reference](/docs/cli-api-reference).

<div class="collection-method-list" markdown="1">

- [`--bail`](#bail)
- [`--dirty`](#dirty)
- [`--filter`](#filter)
- [`--group`](#group)
- [`--exclude-group`](#exclude-group)
- [`--retry`](#retry)
- [`only()`](#only)

</div>

<a name="bail"></a>
### `--bail`

The `--bail` option instructs Pest to stop executing your test suite upon encountering the first failure or error.

```bash
./vendor/bin/pest --bail
```

<a name="dirty"></a>
### `--dirty`

The `--dirty` option instructs Pest to only run tests that have uncommitted changes according to Git. This is often useful when you're developing a set of tests for a new feature and don't want to run the entire test suite each time Pest is invoked.

```bash
./vendor/bin/pest --dirty
```

> Note that, due to a limitation in Pest, test cases written using the PHPUnit syntax will always be considered dirty.

<a name="filter"></a>
### `--filter`

Using the `--filter` option, it is possible to run tests that match a specified regular expression pattern. The `--filter` option allows you to filter tests based on any information that would typically appear in the test's output description, such as the filename, test description, dataset parameters, and more.

```bash
./vendor/bin/pest --filter "test description"
```

<a name="group"></a>
### `--group`

You can utilize the `--group` option to selectively run tests belonging to a particular group. To learn about assigning tests or folders to groups, please refer to the [Grouping Tests](/docs/grouping-tests) documentation.

```bash
./vendor/bin/pest --group=integration
```

In cases where multiple test groups need to be included, you need to use the `--group` option per group.

```bash
./vendor/bin/pest --group=integration --group=browser
```

<a name="exclude-group"></a>
### `--exclude-group`

The `--exclude-group` option may be used to exclude specific test groups from being executed.

```bash
./vendor/bin/pest --exclude-group=integration
```

In cases where multiple test groups need to be excluded, you need to use the `--exclude-group` option per group.

```bash
./vendor/bin/pest --exclude-group=integration --exclude-group=browser
```

<a name="retry"></a>
### `--retry`

If a test previously failed, you typically want to sort the failed tests by arranging the test suite to run them first. In such cases, you can use the `--retry` option.

The `--retry` flag reorders your test suites by prioritizing the previously failed tests. If there were no past failures, the suite runs as usual. But if there were previous failures, those tests are run first.

> Note: Keep in mind that if your `phpunit.xml` file has two test suites (usually Unit and Feature), this option will sort each suite by running the failed tests first. This means that sometimes, you may see the entire Unit test suite run before Pest runs the Feature test suite, where previously failed tests take priority.

```bash
./vendor/bin/pest --retry
```

<a name="only"></a>
### `only()`

During development, you may want to focus on running specific tests while excluding all others. Pest provides two ways to do this: running only tests in a specific file, or running only a specific test within a file.

#### Running Only Tests in a File

When working on a specific feature, you can mark all tests in a file to run exclusively by calling the `only()` function at the top of your test file:

```php
<?php

only();

test('first test', function () {
    // This will run
});

test('second test', function () {
    // This will also run
});
```

All tests in files with `only()` will run, while tests in other files will be skipped.

#### Running a Single Test

To run only a specific test within a file, chain the `->only()` method to the test:

```php
test('sum', function () {
  $result = sum(1, 2);

  expect($result)->toBe(3);
})->only();

test('another test', function () {
  // This will be skipped
});
```

---

As your codebase grows, manually running your tests with filtering can become tedious. That's where skipping tests comes in. Skipping tests is a useful feature that allows developers to exclude specific tests from the test suite temporarily without deleting them entirely: [Skipping Tests →](/docs/skipping-tests)

