---
title: Drift
description: The Drift migration tool guide.
---

# Drift

- [Overview](#overview)
    - [Installation](#installation)
- [Uses](#uses)
    - [Migrate](#migrate)
    - [Polish](#polish)

<a name="overview"></a>
## Overview

**WARNING: This is a 'work in progress'.**

Drift is a command-line tool to migrate your PHPUnit tests to Pest.

<a name="installation"></a>
### Installation

Drift can be installed through Composer like all Pest components using the following command:

```bash
composer require pestphp/drift --dev --update-with-all-dependencies
```

Once installed, it provides a Composer binary that can be executed with `vendor/bin/drift`.

<a name="uses"></a>
## Uses

There are two main commands: `migrate` and `polish`

<a name="migrate"></a>
### Migrate

This command allows you to migrate your tests to the Pest syntax. Behind the scenes, Drift uses [Rector](https://github.com/rectorphp/rector), a tool for refactoring PHP code. Note, these do not follow a specific code style so may require formatting after using your own PHP code style tools.

To view the changes that would be made without applying them (dry-run), use the `--show` flag:

```bash
vendor/bin/drift migrate --show tests
```

Once you have confirmed that the changes are fine, run the command without the flag to apply the changes:

```bash
vendor/bin/drift migrate tests
```

You can then run `vendor/bin/pest` to verify that the changes run as expected.

<a name="polish"></a>
### Polish

The Polish command will apply an additional set of changes to make sure your tests follow Pest best practices.

To view the changes that would be made without applying them (dry-run), use the `--show` flag:

```bash
vendor/bin/drift polish --show tests
```

Once you have confirmed that the changes are fine, run the command without the flag to apply the changes:

```bash
vendor/bin/drift polish tests
```

---

Next section: [Laravel →](/docs/guides/laravel)
