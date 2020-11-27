---
title: Coverage
description: Coverage
---

# Coverage

- [Overview](#overview)
- [Extra Options](#extra-options)
    - [`--coverage`](#coverage)
    - [`--min`](#min)

<a name="overview"></a>
## Overview

**Requires [XDEBUG 2.0+](https://xdebug.org/docs/install/)**, or [PCOV](https://github.com/krakjoe/pcov), or [PHPDBG](https://www.php.net/manual/en/book.phpdbg.php).

Code coverage in Pest tells you which lines of code your test
suite execute and which lines it doesn’t. Of course, PHPUnit
offers you a beautiful section about this topic: [Code Coverage Analysis](https://phpunit.readthedocs.io/en/9.0/code-coverage-analysis.html).

<a name="extra-options"></a>
## Extra Options

Now, in this section, we are going to see the extra options that Pest offers.

<a name="coverage"></a>
### `--coverage`

The `--coverage` option, gives you a human readable code coverage
directly on the console.

```
./vendor/bin/pest --coverage
```

![Coverage](/assets/img/coverage.png)

Keep in mind: the percentage displayed concerns the number of lines
of source code that has been tested/executed.

<a name="min"></a>
### `--min`

Of course, you can always combine the `--coverage` option
with the `--min` option to configure minimum threshold enforcement
for coverage results. If the thresholds are not met, Pest will return failure.

```
./vendor/bin/pest --coverage --min=90
```

![Coverage Min](/assets/img/coverage-min.png)

<a name="filters"></a>
## Filters

By default, the PHPUnit [configuration file generated](https://github.com/pestphp/pest-plugin-init/blob/master/stubs/phpunit.xml) by Pest initialisation will include directory coverage for the `app` and `src` directories.

Although this covers most libraries and applications, you may need to add more directories to be covered. To do this, you'll need to add a new `<directory>` element inside the `coverage.include` section.

If you are experiencing a `Coverage not found in path: vendor/pestphp/.temp/coverage.php` exception message, for example, if your code is under a `lib` directory, you'll need to apply the following change to your `phpunit.xml`.

```diff
<coverage processUncoveredFiles="true">
    <include>
        <directory suffix=".php">./app</directory>
        <directory suffix=".php">./src</directory>
+       <directory suffix=".php">./lib</directory>
    </include>
</coverage>
```

Next section: [Command Line →](/docs/guides/command-line)
