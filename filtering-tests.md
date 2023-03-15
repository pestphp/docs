---
title: Filtering Tests
description: When you run `/vendor/bin/pest`, Pest executes the complete test suite by default. Nevertheless, Pest provides various CLI options and methods that can be helpful for filtering your test suite during development.
---

# Test Filtering

When you run `/vendor/bin/pest`, Pest executes the complete test suite by default. As expected, running individual tests is accomplished by passing the test name as the first argument.

```bash
/vendor/bin/pest tests/Unit/TestExample.php
```

Nevertheless, Pest provides various CLI options and methods that can be helpful for filtering your test suite during development.

<div class="collection-method-list" markdown="1">

- [`--filter`](#filter)
- [`--dirty`](#dirty)
- [`--group`](#group)
- [`--exclude-group`](#exclude-group)
- [`--retry`](#retry)

</div>

<a name="filter"></a>
### `--filter`

Using the `--filter` option, it is possible to run tests that match a specified regular expression pattern. The `--filter` option allows you to filter test results based on any information that would typically appear in the test's output description, such as the filename, test description, dataset parameters, and more.

```bash
/vendor/bin/pest --filter "test description"
```

<a name="dirty"></a>
### `--dirty`

With the `--dirty` option, you may run the modified tests that have uncommitted changes according to Git. This can be useful, when the user is developing a set of tests for a new feature, and don't want to run the entire test suite all the time.

```bash
/vendor/bin/pest --dirty
```

<a name="group"></a>
### `--group`

You can utilize the `--group` option to selectively run tests belonging to a particular group. To learn about assigning a specific test or folder to a group, please refer to the [Grouping Tests →](/docs/grouping-tests) documentation.

```bash
./vendor/bin/pest --group=integration,browser
```

<a name="exclude-group"></a>
### `--exclude-group`

Sometimes, instead of using the `--group` option, you can use the `--exclude-group` option to exclude specific groups from being executed.

```bash
./vendor/bin/pest --group=integration,browser
```

<a name="retry"></a>
### `--retry`

If a test has failed previously, you might want to attempt running it again. In such cases, you can utilize the `--retry` option, which can prove beneficial if you suspect that the failure was a result of a temporary issue or an error on your part. Using this option, you can try the test again and determine if it passes on a subsequent attempt.

```bash
./vendor/bin/pest --retry
```

---

As your codebase grows, manually running your tests with filtering can become tedious. That's where skipping tests comes in. Skipping tests is a useful feature that allows developers to exclude specific tests from the test suite temporarily without deleting them entirely: [Skipping Tests →](/docs/skipping-tests)

