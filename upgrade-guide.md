---
title: Upgrade Guide
description: Upgrading To 2.x From 1.x
---

# Upgrading To 2.x From 1.x

> **Estimated Upgrade Time**: 2 minutes

We make an effort to document every potential breaking change, but some of these changes may exist in less frequently used sections of the framework. As a result, only a subset of these changes may impact your application.

### Updating Dependencies

> Likelihood Of Impact: High

Pest 2 now requires PHP 8.1.0 or greater. To start migrating from Pest 1 to Pest 2, update the `pestphp/pest` dependency to `^2.0` in your application's `composer.json` file.

```diff
-    "pestphp/pest": "^1.22",
+    "pestphp/pest": "^2.0",
```

Next, you can remove PHPUnit from your list of dependencies if it is included.

```diff
-    "phpunit/phpunit": "^9.5.10",
```

In addition, if you are using Laravel, please upgrade Collision to version 7. Note that, Laravel 10 is required.

```diff
-    "nunomaduro/collision": "^6.0",
+    "nunomaduro/collision": "^7.0",
```

If you are using the Parallel Plugin (or Paratest), you may remove it from your dependencies since it is now included with Pest by default.

```diff
-    "brianium/paratest": "^6.8.1",
-    "pestphp/pest-plugin-parallel": "^1.2.1",
```

The Global Assertions Plugin is archived and should be removed from your dependencies.

```diff
-    "pestphp/pest-plugin-global-assertions": "^1.0.0",
```

If you relied on the Global Assertions Plugin, you may access the same underlying assertions using the `$this` variable. Alternatively, you may migrate to the [Expectation API](/docs/expectations).

```diff
test('sum', function () {
    $result = sum(1, 2);

-   assertSame(3, $result);
+   $this->assertSame(3, $result); // or expect($result)->toBe(3)
});
```

All other Pest maintained plugins should be updated to version `^2.0` in your application's `composer.json` file.

```diff
-    "pestphp/pest-plugin-laravel": "^1.4",
+    "pestphp/pest-plugin-laravel": "^2.0",
```

If you are using the Faker Plugin, the `faker()` function has been renamed to `fake()`. You will need to update all uses.

```diff
- use function Pest\Faker\faker;
+ use function Pest\Faker\fake;

test('faker', function () {
-   expect(faker()->name())->toBeString();
+   expect(fake()->name())->toBeString();
});
```

### PHPUnit 10 Changes

> Likelihood Of Impact: Medium

If you were previously using PHPUnit instead of Pest, it's possible that your `phpunit.xml` file needs to be updated. When this is the case, you may encounter the following message when running Pest 2 for the first time.

```php
  WARN  Your XML configuration validates against a deprecated schema. Migrate your XML configuration using "--migrate-configuration"!
```

To address this issue, simply re-run Pest with the `--migrate-configuration` option.

```bash
./vendor/bin/pest --migrate-configuration
```

Pest 2 is built on top of PHPUnit 10. This means that any notable changes made to PHPUnit 10 might have an impact on your test suite. To examine all the changes introduced in PHPUnit 10, please consult the [PHPUnit 10 changelog](https://github.com/sebastianbergmann/phpunit/blob/10.0.0/ChangeLog-10.0.md#1000---2023-02-03).

### High Order Testing

> Likelihood Of Impact: Low

When performing high order testing, you might have utilized the `tap()` method to invoke assertions on an object that needs lazy evaluation during runtime. With Pest 2, the `tap()` method is deprecated. Instead, you should use the `defer()` method.

```diff
it('creates admins')
-    ->tap(fn () => $this->artisan('user:create --admin'))
+    ->defer(fn () => $this->artisan('user:create --admin'))
     ->assertDatabaseHas('users', ['id' => 1]);
```

### Datasets

#### Bound Datasets

> Likelihood Of Impact: Very low

If you are utilizing "bound" datasets and binding a single dataset argument, you must now type the corresponding test parameter.

```diff
-it('can generate the full name of a user', function ($user, $fullName) {
+it('can generate the full name of a user', function (User $user, $fullName) {
    expect($user->full_name)->toBe($fullName);
})->with([
    [fn() => User::factory()->create(['first_name' => 'Nuno', 'last_name' => 'Maduro']), 'Nuno Maduro'],
    [fn() => User::factory()->create(['first_name' => 'Luke', 'last_name' => 'Downing']), 'Luke Downing'],
    [fn() => User::factory()->create(['first_name' => 'Freek', 'last_name' => 'Van Der Herten']), 'Freek Van Der Herten'],
]);
```

#### Scoped Datasets

> Likelihood Of Impact: Very low

Although we previously documented in Pest 1 that datasets should only be declared using the `dataset` function in the `tests/Pest.php` or `tests/Datasets.php` files, you could actually declare datasets in any test file within your test suite. However, in Pest 2, with the introduction of [scoped datasets](/docs/datasets#content-scoped-datasets), datasets declared in a test file can only be utilized within that same test file. Therefore, if you have a dataset that needs to be accessible globally, please ensure that it is placed in either the `tests/Pest.php` or `tests/Datasets.php` files.

---

This concludes the Pest 2 upgrade guide. On the next chapter, we'll cover how can you easily migrate your tests from PHPUnit to Pest: [Migrating From PHPUnit](/docs/migrating-from-phpunit-guide)
