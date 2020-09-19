---
title: Coverage
description: Coverage
---

# Coverage

- [Introduction](#introduction)
- [Extra Options](#extra-options)

<a name="introduction"></a>
## Introduction

**Requires [XDEBUG 2.0+](https://xdebug.org/docs/install/)**, or [PCOV](https://github.com/krakjoe/pcov), or [PHPDBG](https://www.php.net/manual/en/book.phpdbg.php).

Code coverage in Pest tells you which lines of code your test
suite execute and which lines it doesn’t. Of course, PHPUnit
offers you a beautiful section about this topic: [Code Coverage Analysis](https://phpunit.readthedocs.io/en/9.0/code-coverage-analysis.html).

<a name="extra-options"></a>
## Extra Options

Now, in this section, we are going to see the extra options that Pest offers.

### `--coverage`

The `--coverage` option, gives you a human readable code coverage
directly on the console.

```
./vendor/bin/pest --coverage
```

![Coverage](/assets/img/coverage.png)

Keep in mind: the percentage displayed concerns the number of lines
of source code that has been tested/executed.

### `--min`

Of course, you can always combine the `--coverage` option
with the `--min` option to configure minimum threshold enforcement
for coverage results. If the thresholds are not met, Pest will return failure.

```
./vendor/bin/pest --coverage --min=90
```

![Coverage Min](/assets/img/coverage-min.png)

Next section: [PHPUnit →](/docs/guides/phpunit)
