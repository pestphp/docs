---
title: Watch Plugin
description: The Watch Plugin
---

# Watch Plugin

- [Overview](#overview)
- [Installation](#installation)
- [Running Watch](#running-watch)
- [Default directories](#default-directories)
- [Custom directories](#custom-directories)

<a name="overview"></a>
## Overview

The Watch plugin keeps an eye on your application and automatically runs your Pest tests when you change files inside specific [directories](#default-directories).

> Note: This is still a development package and needs further testing.

<a name="installation"></a>
## Installation

First, [**install fswatch**](https://github.com/emcrisostomo/fswatch#getting-fswatch) on your machine, or check if you already have it:

```bash
fswatch --version
```

Then, install the Watch Plugin via the Composer package manager:

```bash
composer require pestphp/pest-plugin-watch --dev
```

Once the installation is finished, your Pest CLI will have a new `--watch`  option available.

<a name="running-watch"></a>
## Running Watch

Running `pest --watch` starts Pest in watch mode and, from this point on, any file modification inside the [directories](#default-directories) will trigger Pest to run your tests.

```bash
pest --watch # Night gathers, and now my watch begins
```

<a name="default-directories"></a>
## Default directories

By default, the Watch plugin observes the following directories:

- `tests/`
- `app/`
- `src/`

<a name="custom-directories"></a>
## Custom directories

If you need to watch a custom set of directories, just provide a comma-separated list to the `--watch` flag.

Example:

```bash
./vendor/bin/pest --watch=app,routes,tests
```

The command will watch the `app`, `routes`, and `tests` directories.

## Filter test

You still can filter the test you want to run during the watching loop by using the `--filter=` option. 

However, the test name should be wrapped into `"` in this case, like this:
```bash
./vendor/bin/pest --watch --filter="'can run this test on watching'"
```

You can create an alias or a bash function to run this easily: 

```bash
function pw {
 ./vendor/bin/pest --filter="\"$1\"" --watch
}
```

So now, to watch a test you simply run: 

```
pw 'can run this test on watching'
```


Next section: [Creating Plugins →](/docs/plugins/creating-plugins)
