---
title: Global Hooks
description: Define hooks once in your Pest.php configuration file and share them across your entire test suite, keeping repetitive setup and teardown free of duplication.
---

# Global Hooks

As you may recall, hooks simplify your testing process and automate repetitive tasks that you perform before or after a test. However, when the same hooks are repeated across multiple test files, you may wish to define "global" hooks to avoid duplication. You may define global hooks within your `Pest.php` configuration file.

For instance, if you need to perform some database operations before each test within the `Feature` folder, you may use the `beforeEach()` hook within your `Pest.php` configuration file:

```php
pest()->extend(TestCase::class)->beforeEach(function () {
    // Interact with your database...
})->group('integration')->in('Feature');
```

In addition, you may define global hooks that will run before or after your entire test suite, regardless of the folder or group:

```php
pest()->beforeEach(function () {
    // Interact with your database...
});
```

In fact, any of the hooks mentioned in the [hooks](/docs/hooks) documentation may also be used within your `Pest.php` configuration file:

```php
pest()->extend(TestCase::class)->beforeAll(function () {
    // Runs before each file...
})->beforeEach(function () {
    // Runs before each test...
})->afterEach(function () {
    // Runs after each test...
})->afterAll(function () {
    // Runs after each file...
})->group('integration')->in('Feature');
```

Any `before*` hooks defined in the `Pest.php` configuration file will run prior to the hooks defined in individual test files. Similarly, any `after*` hooks defined in the `Pest.php` configuration file will run after the hooks defined in individual test files.

---

Next, let's look at how to reduce duplication across your test suite by extracting reusable logic into custom helper functions: [Custom Helpers](/docs/custom-helpers)
