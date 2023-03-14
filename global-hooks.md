---
title: Global Hooks
description: Global HOoks
---

# Global Hooks

As previously stated, hooks allow you to simplify your testing process and automate repetitive tasks that you may perform before or after a test. It's worth noting that if the tasks are the same across multiple test files, you can define "global" hooks in your `Pest.php` configuration file.

For instance, if you need to execute a certain task in your database before each test within the `Feature` folder, you can make use of the `beforeEach` hook. This hook will run the specified task before each test within the folder.

```php
uses(TestCase::class)->beforeEach(function () {
    // interact with your database...
})->group('integration')->in('Feature');
```

In addition, you have the option to specify hooks that will run before or after your entire test suite, regardless of the folder or group.

```php
uses()->beforeEach(function () {
    // interact with your database...
})->in(__DIR__); // all folders, and all groups...
```

It should come as no surprise that any of the other hooks mentioned in the [Hooks](/docs/hooks) documentation can also be used at the top level in your `Pest.php` configuration file.

```php
uses(TestCase::class)->beforeAll(function () {
    // runs before each file...
})->beforeEach(function () {
    // runs before each test...
})->afterEach(function () {
    // runs after each test...
})->afterAll(function () {
    // runs after each file...
})->group('integration')->in('Feature');
```

Now, in terms of ordering, `before*` hooks specified at the `Pest.php` configuration level will be executed prior to any hook specified at the file level. Conversely, `after*` hooks specified at the Pest.php configuration level will be executed subsequent to any hook specified at the file level.

---

When setting up a test suite, it may be necessary to mock certain functionality or objects in order to isolate the code being tested and to simulate certain conditions or behaviors. This can be done through the use of mocking libraries or frameworks, such as Mockery: [Mocking](/docs/mocking)
