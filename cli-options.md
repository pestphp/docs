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

Pest defers non-Pest-specific options to PHPUnit, so please refer to [the PHPUnit command-line test runner](https://phpunit.readthedocs.io/en/latest/textui.html#command-line-options) documentation for a full list.

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

This option allows you to only run a specific list of [grouped tests](/docs/groups). This is a comma-separated list.

---

Next section: [Infection Support →](/docs/infection)
