---
title: Snapshots Plugin
description: The Snapshots Plugin
---

# Snapshots Plugin

- [Overview](#overview)
- [Installation](#installation)
- [Available Functions](#available-functions)

<a name="overview"></a>
## Overview

The Snapshots Plugin for Pest adds [snapshot](https://github.com/spatie/phpunit-snapshot-assertions) testing capabilities to Pest.

**Source code**: [github.com/spatie/pest-plugin-snapshots](https://github.com/spatie/pest-plugin-snapshots)

Snapshot tests are a very useful tool whenever you want to make sure things don't change unexpectedly. Find more
about snapshot testing here: [sebastiandedeyne.com/a-package-for-snapshot-testing-in-phpunit/](https://sebastiandedeyne.com/a-package-for-snapshot-testing-in-phpunit/)

<a name="installation"></a>
### Installation

Install the Snapshots Plugin via the Composer package manager:

```bash
composer require spatie/pest-plugin-snapshots --dev
```

<a name="available-functions"></a>
## Available functions

<div class="collection-method-list" markdown="1">

- [`assertMatchesSnapshot()`](#assertMatchesSnapshot)
- [`assertMatchesFileHashSnapshot()`](#assertMatchesSnapshot)
- [`assertMatchesFileSnapshot()`](#assertMatchesSnapshot)
- [`assertMatchesHtmlSnapshot()`](#assertMatchesSnapshot)
- [`assertMatchesJsonSnapshot()`](#assertMatchesSnapshot)
- [`assertMatchesObjectSnapshot()`](#assertMatchesSnapshot)
- [`assertMatchesTextSnapshot()`](#assertMatchesSnapshot)
- [`assertMatchesXmlSnapshot()`](#assertMatchesSnapshot)
- [`assertMatchesYamlSnapshot()`](#assertMatchesSnapshot)

</div>

<a name="assertMatchesSnapshot"></a>
### `assertMatchesSnapshot()`

The `assertMatchesSnapshot()` function asserts the given string matches the existing snapshot.

```php
use function Spatie\Snapshots\assertMatchesSnapshot;
use function Pest\Laravel\get;

it('renders correctly', function () {
    $html = get('/')->getContent();

    assertMatchesSnapshot($html);
});
```

If you’re working with specific data like JSON or XML, you’re better off using a dedicated `assertMatchesJsonSnapshot` or `assertMatchesXmlSnapshot` method, which will save snapshots as `.json` or `.xml` files, and provide a better diff when the snapshot doesn’t match:

- `assertMatchesSnapshot()`
- `assertMatchesFileHashSnapshot()`
- `assertMatchesFileSnapshot()`
- `assertMatchesHtmlSnapshot()`
- `assertMatchesJsonSnapshot()`
- `assertMatchesObjectSnapshot()`
- `assertMatchesTextSnapshot()`
- `assertMatchesXmlSnapshot()`
- `assertMatchesYamlSnapshot()`

Note that, by default, snapshots are stored in a `tests/__snapshots__` directory.

Also, when you expect a changed value, you may need to run the `-d --update-snapshots` flag to update the existing snapshots:
```
./vendor/bin/pest -d --update-snapshots
```

---

Next section: [Watch Plugin →](/docs/plugins/watch)
