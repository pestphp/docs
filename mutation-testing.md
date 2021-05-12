---
title: Mutation Testing
description: Mutation Testing
---

# Mutation Testing with Infection

- [Overview](#overview)
- [Installation](#installation)

<a name="overview"></a>
## Overview

Mutation testing is the least common and most misunderstood type of testing. With mutation testing, the code under test is changed to create a faulty version called a mutant. You then run the mutant code through a suite of test cases, which should produce new test case failures. If no new failures appear, the test suite most likely does not exercise the code path containing the mutated code, which means the code isn't fully tested.

Performing mutation testing with Pest couldn't be easier as the most popular mutation testing framework - **[Infection](https://infection.github.io/guide/)** - includes out-of-the-box support for Pest.

<a name="installation"></a>
## Installation

> **Requirements:** [Pest 1.2+](https://pestphp.com/docs/upgrade-guide/), [Infection 0.23+](https://github.com/infection/infection/blob/master/CHANGELOG.md)

First, install Infection via the Composer package manager:

```bash
composer require infection/infection --dev
```

Then, you can run Infection directly from the command line:

```bash
./vendor/bin/infection --test-framework=pest --show-mutations

# With XDebug
XDEBUG_MODE=coverage ./vendor/bin/infection --test-framework=pest --show-mutations
```

---

Next section: [Laravel Plugin →](/docs/plugins/laravel)
