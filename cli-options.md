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

To list all Pest and PHPUnit options run:

```bash
./vendor/bin/pest --help
```

Please refer to [PHPUnit command-line test runner](https://phpunit.readthedocs.io/en/9.5/textui.html) for a list of non-Pest-specific options.

<a name="available-options"></a>

## Available Options

### `--init`

This option allows you to initialize a standard Pest configuration. Read more about [Files & Folders](/docs/files-and-folders) in Pest.

### `--coverage`

This option enables coverage using the underlying `--coverage-php` option, and will output the results to the terminal's standard output.

You can combine this with other coverage options from PHPUnit such as `--coverage-xml` or `--coverage-html`.

### `--min`

This option allows you to set a minimum required coverage value. If the coverage does not meet this value, the test suite will fail.

### `--group`

This option allows you to only run a specific list of [grouped tests](/docs/groups). This is a comma-separated list.

### `--exclude-group`

You may also use the `--exclude-group` option to instruct Pest to exclude [grouped tests](/docs/groups). This is a comma-separated list.

### `--test-directory`

This option allows you to specify the directory that will be used to gather the tests that should be run. If it isn't provided, the default `tests` directory will be used.

### `--ci`

You may use the `--ci` option to instruct Pest that the test suite is running within a Continuous Integration environment. When provided, this option makes Pest ignore development/local features such as [->only()](/docs/running-a-single-test).

---

Next section: [Laravel Plugin →](/docs/plugins/laravel)

