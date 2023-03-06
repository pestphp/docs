---
title: Grouping Tests
description: Groups Of Tests
---

# Grouping Tests

- [Overview](#overview)

<a name="overview"></a>
## Overview

Optionally, Pest allows you to assign tests to different groups with the `group` method. If you have a bunch of
particularly slow tests, it might be good to add them all to the same group:

```php
it('has home', function () {
    // ..
})->group('integration');
```

Of course, you can also assign a test to multiple groups:

```php
it('has home', function () {
    // ..
})->group('integration', 'browser');
```

Sometimes, you may want to assign an entire file to a group:

```php
uses()->group('integration');
```

Or a specific folder:

```php
// Pest.php
uses()->group('integration')->in('integration');
```

Finally, you can run the tests of a specific group using the `--group` option while
running Pest on the command-line:

```bash
./vendor/bin/pest --group=integration,browser
```

You may also exclude specific groups using the  `--exclude-group` option:

```bash
./vendor/bin/pest --exclude-group=api
```

> **Note:** The `uses()->group('integration')->in('Feature')` will **not** put any PHPUnit test class under the *integration* group.
You still need the `@group` [annotation](https://phpunit.readthedocs.io/en/9.5/annotations.html) for them.
Pest will understand it.

---

Next section: - [Test Dependency →](/docs/test-dependency)