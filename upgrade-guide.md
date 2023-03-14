---
title: Upgrade Guide
description: Upgrading To 2.x From 1.x
---

# Upgrading To 2.x From 1.x

> **Estimated Upgrade Time**: 2 minutes

We make an effort to document every potential breaking change, but some of these changes may exist in less frequently used sections of the framework. As a result, only a subset of these changes may impact your application.

### Updating Dependencies

> Likelihood Of Impact: High

Pest v2 requires PHP 8.1.0 or greater. To start the migration process from Pest v1 to v2, please update the `pestphp/pest` dependency to `^2.0` in your `composer.json` file.

```diff
-    "pestphp/pest": "^1.22",
+    "pestphp/pest": "^2.0",
````

If you are using the Parallel Plugin, please remove it from your dependencies since it has been merged into Pest's core.

```diff
-    "pestphp/pest-plugin-parallel": "^1.2.1",
````

The Global Assertions Plugin is now archived and therefore, you must remove it from your dependencies.

```diff
-    "pestphp/pest-plugin-global-assertions": "^1.0.0",
````

If you relied on the Global Assertions Plugin, please access the underlying assertions using the `$this` variable. Alternatively, migrate to the [Expectation API](/docs/expectations) for your testing needs.

```diff
test('sum', function () {
    $result = sum(1, 2);

-   assertSame(3, $result);
+   $this->assertSame(3, $result); // or expect($result)->toBe(3)
});
```

Of course, for any other plugins, please ensure that you upgrade them to version ^2.0 in your composer.json file.

```diff
-    "pestphp/pest-plugin-laravel": "^1.4",
+    "pestphp/pest-plugin-laravel": "^2.0",
```

### PHPUnit 10 Changes

> Likelihood Of Impact: Medium

In case you were previously using PHPUnit instead of Pest, it's possible that your phpunit.xml file needs to be updated. If this applies to you, then you may encounter the following message when running Pest v2 for the first time.

```php
  WARN  Your XML configuration validates against a deprecated schema. Migrate your XML configuration using "--migrate-configuration"!
```

To address this issue, and as detailed on the warning, simply re-run Pest with the `--migrate-configuration` option.

```bash
./vendor/bin/pest --migrate-configuration
```

Furthermore, it's worth noting that Pest v2 is built on top of PHPUnit v10. This means that any notable changes made to PHPUnit v10 might have an impact on your test suite. To examine all the changes introduced in PHPUnit v10, please refer to the PHPUnit v10 Changelog at the following link:[PHPUnit v10 Changelog](https://github.com/sebastianbergmann/phpunit/blob/10.0.0/ChangeLog-10.0.md#1000---2023-02-03).

### High Order Testing

> Likelihood Of Impact: Low

Occasionally, when doing high order testing, you might have utilized the `tap` method to invoke assertions on an object that needs lazy evaluation during runtime. With v2, the `tap` method is now deprecated, and in such instances, you should use the `defer` method.

```diff
it('creates admins')
-    ->tap(fn () => $this->artisan('user:create --admin'))
+    ->defer(fn () => $this->artisan('user:create --admin'))
     ->assertDatabaseHas('users', ['id' => 1]);
```

### Datasets

> Likelihood Of Impact: Very low

Although we previously documented in Pest v1 that datasets should only be declared using the `dataset` function in the `tests/Pest.php` file or `tests/Datasets.php`, the truth is that you could declare them in any test file within your test suite. However, in Pest v2, with the introduction of [Scope Datasets](/docs/datasets#scope-datasets), datasets declared in a test file can only be utilized within that same test file. Therefore, if you have a dataset that needs to be accessible globally, please ensure that it is placed in either the `tests/Pest.php` or `tests/Datasets.php` files.

### `->only()`

> Likelihood Of Impact: Very Low

In the past, you may have used the `->only()` method to only run a particular test in your test suite. This method no longer exists. As workaround, you may use Pest's new CLI options: `--dirty` or `--retry`.

```php
test('sum', function () {
   $result = sum(1, 2);

   expect($result)->toBe(3);
-})->only();
+});
```

---

Next section: [Community →](/docs/community)
