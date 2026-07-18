---
title: Migrating from PHPUnit
description: Migrating from PHPUnit to Pest is a simple process you may complete in a few steps.
---

# Migrating from PHPUnit

Pest is built on top of PHPUnit, so migrating from PHPUnit to Pest is a simple process you may complete in a few steps. Once you have Pest installed, you should require the `pestphp/pest-plugin-drift` package as a "dev" dependency in your project:

```bash
composer require pestphp/pest-plugin-drift --dev
```

Drift is a simple, yet powerful plugin that will automatically convert your PHPUnit tests to Pest when you run the `--drift` option:

```bash
./vendor/bin/pest --drift
```

Typically, a PHPUnit test looks like this:

```php
<?php

namespace Tests\Unit;

use PHPUnit\Framework\TestCase;

class ExampleTest extends TestCase
{
    public function test_that_true_is_true(): void
    {
        $this->assertTrue(true);
    }
}
```

After running `--drift`, it will look like this:

```php
test('true is true', function () {
    expect(true)->toBeTrue();
});
```

## Converting Tests Within a Specific Folder

Sometimes you may wish to convert the PHPUnit tests within a certain folder only. To accomplish this, you may pass a path as the first argument when calling `--drift`. For example, you may run the conversion for the `tests/Helpers` folder:

```bash
./vendor/bin/pest --drift tests/Helpers
```

The output will contain a summary of the conversion process, as well as a list of the files that were converted:

```plain
./vendor/bin/pest --drift tests/Helpers

✔✔✔✔✔✔✔✔✔✔✔✔✔✔✔✔✔✔✔✔✔✔✔✔✔✔

INFO  The [tests/Helpers] directory has been migrated to PEST with XY files changed.
```

While most of your tests will be converted automatically, and you should be able to run them without any issues, there are some cases where you may need to convert a few of your tests manually.

---

Of course, this particular chapter is only for those who are migrating from PHPUnit. Next, let's learn how you may contribute to the growth of Pest: [Community Guide](/docs/community-guide)
