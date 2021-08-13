---
title: Parallel Plugin
description: The Parallel Plugin
---

# Parallel Plugin

- [Overview](#overview)
- [Installation](#installation)
- [Usage](#usage)
- [Laravel](#laravel)

<a name="overview"></a>
## Overview

Pest is the first testing framework to ship with first-party support for parallel testing. Using this feature,
you can drastically reduce the time it take to run large test suites.

**Source code**: [github.com/pestphp/pest-plugin-parallel](https://github.com/pestphp/pest-plugin-parallel)

<a name="installation"></a>
## Installation

Install the Parallel Plugin via the Composer package manager:

```bash
composer require pestphp/pest-plugin-parallel --dev
```

<a name="usage"></a>
## Usage

Once installed, running your tests in parallel is as simple as tagging `--parallel` or `-P` onto the standard pest command:

```bash
./vendor/bin/pest --parallel

# Shorthand...
./vendor/bin/pest -P
```

By default, the Parallel plugin will consume as many processes as you have threads on your computer. You can 
always tweak this manually with the `-p` option:

```bash
# Use 8 processes to run the test suite
./vendor/bin/pest --parallel -p=8
```

The parallel plugin has been fine-tuned to work seamlessly with other Pest options. For example, displaying test coverage
is identical to running Pest normally:

```bash
./vendor/bin/pest --parallel --coverage --min=90
```

<a name="laravel"></a>
## Laravel

If you're used to running your Laravel test suite using `php artisan test --parallel`, you don't need to 
do anything else. Pest is intelligent enough to detect that you want to run your Pest tests instead of
PhpUnit tests.

---

Next section: [Snapshots Plugin →](/docs/plugins/snapshots)
