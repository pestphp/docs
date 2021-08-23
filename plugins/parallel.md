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

By default, Pest executes your tests sequentially within a single process. 
However, you may save a lot of time by using the Parallel plugin, which allows you to run tests simultaneously across multiple processes.

**Source code**: [github.com/pestphp/pest-plugin-parallel](https://github.com/pestphp/pest-plugin-parallel)

<a name="installation"></a>
## Installation

Install the Parallel Plugin via the Composer package manager:

```bash
composer require pestphp/pest-plugin-parallel --dev
```

<a name="usage"></a>
## Usage

Once installed, running your tests in parallel is as simple as using the `--parallel` or `-p` option with the standard pest command:

```bash
./vendor/bin/pest --parallel

# Shorthand...
./vendor/bin/pest -p
```

By default, the Parallel plugin will consume as many processes as you have CPU cores on your computer. You can 
always tweak this manually with the `--processes` option:

```bash
# Use 8 processes to run the test suite
./vendor/bin/pest --parallel --processes=8
```

The parallel plugin has been fine-tuned to work seamlessly with other Pest options. For example, displaying test coverage
is identical to running Pest normally:

```bash
./vendor/bin/pest --parallel --coverage --min=90
```

<a name="laravel"></a>
## Laravel

> **Note:** For a seamless experience, make sure you're running Laravel 8.55.0 or higher.

If you're used to running your Laravel test suite with `php artisan test --parallel`, you don't need to 
do anything else. Pest is intelligent enough to detect that you want to run your Pest tests instead of
PHPUnit tests.

---

Next section: [Mock Plugin →](/docs/plugins/mock)
