---
title: Snapshots Plugin
description: The Snapshots Plugin
---

# Snapshots Plugin

The Snapshots Plugin for Pest adds [snapshot](https://github.com/spatie/phpunit-snapshot-assertions) testing capabilities to Pest.

**Source code**: [github.com/spatie/pest-plugin-snapshots](https://github.com/spatie/pest-plugin-snapshots)

Snapshot tests are a very useful tool whenever you want to make sure things don't change unexpectedly. Find more
about snapshot testing here: [sebastiandedeyne.com/a-package-for-snapshot-testing-in-phpunit/](https://sebastiandedeyne.com/a-package-for-snapshot-testing-in-phpunit/)

Install the plugin using Composer:

```bash
composer require spatie/pest-plugin-snapshots --dev
```

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

Next section: [Changelog →](/docs/changelog)
