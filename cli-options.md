---
title: CLI Options
description: Documentation of the available command-line options.
---

# CLI Options

- [Overview](#overview)
- [Available Options](#available-options)

<a name="overview"></a>

## Overview

Pest offers a variety of options through its command line runner to enhance your testing experience. To view all Pest and PHPUnit options, use the command:

```bash
./vendor/bin/pest --help
```

For a comprehensive list of options that are not specific to Pest, we highly recommend consulting the PHPUnit command-line test runner documentation, which can be found at the following link: https://phpunit.readthedocs.io/en/10.0/textui.html.

<a name="available-options"></a>

## Available Options

Pest offers several options to customize and enhance your testing experience. These options are as follows:

### `--init`

This option allows you to initialize a standard Pest configuration. For a deeper understanding of how files and folders are utilized within Pest, please refer to the Files & Folders section of the Pest documentation.

### `--coverage`

Enabling this option allows for coverage testing using the underlying `--coverage-php` option. The results will be output to the standard output of the terminal. Additional coverage options from PHPUnit, such as `--coverage-xml` or `--coverage-html`, can also be combined with this option.

### `--min`

This option allows you to set a minimum required coverage value. If the coverage does not meet this value, the test suite will fail.

### `--group`

This option allows you to run only a specific list of grouped tests. This is a comma-separated list.

### `--exclude-group`

This option allows you to exclude grouped tests from being run. This is a comma-separated list.

### `--test-directory`

This option allows you to specify the directory that will be used to gather the tests that should be run. If it is not provided, the default tests directory will be used.

### `--ci`

This option can be used to instruct Pest that the test suite is running within a Continuous Integration environment. When provided, this option makes Pest ignore development/local features such as `->only()`.

---

Next section: [Laravel Plugin →](/docs/plugins/laravel)
