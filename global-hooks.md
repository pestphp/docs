---
title: Global Hooks
description: As previously discussed, hooks allow you to simplify your testing process and automate repetitive tasks that you may perform before or after a test. However, if the hooks are the same across multiple test files, you may wish to define "global" hooks to avoid code duplication. Global hooks are defined in your `Pest.php` configuration file.
---

# Global Hooks

As previously discussed, hooks allow you to simplify your testing process and automate repetitive tasks that you may perform before or after a test. However, if the hooks are the same across multiple test files, you may wish to define "global" hooks to avoid code duplication. Global hooks are defined in your `Pest.php` configuration file.

For instance, if you need to perform some database operations before each test within the `Feature` folder, you may use the `beforeEach()` hook within your `Pest.php` configuration file.

```php
uses(TestCase::class)->beforeEach(function () {
    // Interact with your database...
})->group('integration')->in('Feature');
```

In addition, you can define global hooks that will run before or after your entire test suite, regardless of the folder or group.

```php
uses()->beforeEach(function () {
    // Interact with your database...
})->in(__DIR__); // All folders, and all groups...
```

In fact, any of the hooks mentioned in the [hooks](/docs/hooks) documentation can also be used in your `Pest.php` configuration file.

```php
uses(TestCase::class)->beforeAll(function () {
    // Runs before each file...
})->beforeEach(function () {
    // Runs before each test...
})->afterEach(function () {
    // Runs after each test...
})->afterAll(function () {
    // Runs after each file...
})->group('integration')->in('Feature');
```

Any `before*` hooks defined in the `Pest.php` configuration file will be executed prior to hooks defined in individual test files. Similarly, any `after*` hooks specified in the `Pest.php` configuration file will be executed after any hooks defined in individual test files.

---

When setting up a test suite, it may be necessary to mock certain functionality or objects in order to isolate the code being tested and to simulate certain conditions or behaviors. This can be done through the use of mocking libraries or frameworks, such as Mockery: [Mocking](/docs/mocking)
