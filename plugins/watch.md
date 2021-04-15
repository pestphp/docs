---
title: Watch Plugin
description: The Watch Plugin
---

# Watch Plugin

> Note: This is still a development package and needs further testing.

The Watch plugin for Pest keeps an eye on your `tests/`, `app/`, and `src/` directories by default and automatically run your Pest tests on file change.

Follow these two steps to install it:

1. [**Install fswatch**](https://github.com/emcrisostomo/fswatch#getting-fswatch) on your machine, or check if you already have it:

```bash
fswatch --version
```

2. Install the Watch plugin using Composer:

```bash
composer require pestphp/pest-plugin-watch --dev
```

This will add a new `--watch` option to your Pest CLI so you can start watching any updates to your code.

**Just start Pest with the following command:**

```bash
pest --watch # Night gathers, and now my watch begins
```

To watch a custom set of directories, provide a comma-separated list to the `--watch` flag as shown below:

```bash
pest --watch=lib,tests
```

Next section: [Creating Plugins →](/docs/plugins/creating-plugins)
