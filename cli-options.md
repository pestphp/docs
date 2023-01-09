---
title: CLI Options
description: Documentation of the available command-line options.
---

# CLI Options

- [Overview](#overview)
- [Available Options](#available-options)

<a name="overview"></a>
## Overview

The Pest command line runner has many available options that can make your testing experience easier.

<a name="available-options"></a>
## Available Options

Pest defers non-Pest-specific options to PHPUnit, so please refer to [the PHPUnit command-line test runner](https://phpunit.readthedocs.io/en/9.5/textui.html#command-line-options) documentation for a full list.

### `--init`

This option allows you to initialize a standard Pest configuration, with the following files:

- A `tests` directory.
- A `phpunit.xml` file that contains the PHPUnit configuration file.
- A `tests/Pest.php` file that contains the Pest configuration file.
- A `tests/ExampleTest.php` file, with a default example test.

### `--coverage`

This option enables coverage using the underlying `--coverage-php` option, and will output the results to the terminals standard output.

You can combine this with other coverage options from PHPUnit such as `--coverage-xml` or `--coverage-html`.

### `--min`

This option allows you to set a minimum required coverage value. If the coverage does not meet this value, the test suite will fail.

### `--group`

This option allows you to only run a specific list of [grouped tests](/docs/groups).

Groups must be passed as a comma-separated list. For example: `--group=integration,browser`

### `--exclude-group`

You may also use the `--exclude-group` option to instruct Pest to exclude [grouped tests](/docs/groups).

Groups must be passed as a comma-separated list. For example: `--exclude-group=integration,browser`

### `--order-by`

This option allows you to run your tests in a specific order.

You may run your tests in a random order using: `--order-by random`. This is useful to guarantee that there are no interdependencies between tests.

Read more about this option at the [PHPUnit documentation](https://phpunit.readthedocs.io/en/9.5/textui.html#command-line-options).

### `--test-directory`

This option allows you to specify the directory that will be used to gather the tests that should be run. If it isn't provided the default `tests` directory will be used.

### `--ci`

You may use the `--ci` option to instruct Pest that the test suite is running within a Continuous Integration environment. When provided, this option makes Pest ignore development/local features such as [->only()](/docs/skipping-tests#running-single-test).

---

Next section: [Laravel Plugin →](/docs/plugins/laravel)

