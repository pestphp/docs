---
title: Hooks
description: Hooks let you run setup and teardown actions before and after each test or file, keeping your tests clean and free of repetitive preparation.
---

# Hooks

Pest hooks are similar to the steps you might take when preparing a meal: first, you gather and prepare the ingredients, then you cook the meal, and finally, you clean up after yourself. In the same way, hooks allow you to perform specific actions before and after each test or file, such as setting up test data, initializing the test environment, or cleaning up resources once the tests are complete.

By using hooks in Pest, you may streamline your testing process and automate repetitive tasks. Whether you are writing unit tests for a small project or building a complex test suite for a large application, hooks help you save time and improve the quality of your tests.

In addition, if you wish to run a hook only for a specific group of tests, you may place the hook within a `describe()` function:

```php
beforeEach(function () {
    //
});

describe('something', function () {
    beforeEach(function () {
        //
    });

    //

    describe('something else', function () {
        beforeEach(function () {
            //
        });

        //
    });
});

test('something', function () {
    //
});
```

Here is a list of the hooks available in Pest:

<div class="collection-method-list" markdown="1">

- [`beforeEach()`](#beforeeach)
- [`afterEach()`](#aftereach)
- [`beforeAll()`](#beforeall)
- [`afterAll()`](#afterall)

</div>

<a name="beforeeach"></a>
## `beforeEach()`

The `beforeEach()` hook executes the provided closure before every test within the current file, ensuring that any necessary setup or configuration is completed before each test:

```php
beforeEach(function () {
    // Prepare something before each test run...
});
```

When using the `beforeEach()` hook, you may initialize properties that will be shared across all tests within the current file. For example, you may use `beforeEach()` to initialize the `$repository` property before each test runs, ensuring that it is available for the subsequent tests in the file:

```php
beforeEach(function () {
    $this->userRepository = new UserRepository();
});

it('may be created', function () {
    $user = $this->userRepository->create();

    expect($user)->toBeInstanceOf(User::class);
});
```

<a name="aftereach"></a>
## `afterEach()`

The `afterEach()` hook executes the provided closure after every test within the current file, allowing you to clean up any resources or state that may have been modified during testing:

```php
afterEach(function () {
    // Clear testing data after each test run...
});
```

So, using the example above, if the `beforeEach()` hook is used to initialize the `$userRepository` property, the `afterEach()` hook may be used to "clean" it after each test when necessary. This ensures that any resources the object may be using are released or reset between tests, preventing any interference or unwanted behavior:

```php
afterEach(function () {
    $this->userRepository->reset();
});
```

Optionally, you may use the `after()` method to perform clean-up tasks after a specific test. This is helpful when you need to clean up resources that are specific to a single test, rather than shared across all tests in the file:

```php
it('may be created', function () {
    $this->userRepository->create();

    expect($user)->toBeInstanceOf(User::class);
})->after(function () {
    $this->userRepository->reset();
});
```

<a name="beforeall"></a>
## `beforeAll()`

The `beforeAll()` hook executes the provided closure once before any tests are run within the current file, allowing you to perform any necessary setup or initialization that applies to all tests:

```php
beforeAll(function () {
    // Prepare something once before any of this file's tests run...
});
```

It is important to note that, unlike the `beforeEach()` hook, the `$this` variable is not available in the `beforeAll()` hook. This is because the hook runs before any tests are executed, so there is no instance of the test class or object to which the variable could refer.

<a name="afterall"></a>
## `afterAll()`

The `afterAll()` hook executes the provided closure once after all tests have completed within the current file, allowing you to perform any necessary clean-up or tear-down tasks:

```php
afterAll(function () {
    // Clean testing data after all tests run...
});
```

Just like the `beforeAll()` method, the `$this` variable is not available in the `afterAll()` hook. This is because the `afterAll()` hook runs after all tests in the file have completed, so there is no longer a test instance or object to which the variable could refer.

---

Once you have mastered using hooks to set up preconditions and clean-up actions for your tests, we are ready to discuss "Datasets", which allow you to run the same test with different inputs or parameters. Datasets let you thoroughly test your code under a variety of conditions and edge cases, helping you identify and fix bugs that may not be immediately obvious: [Datasets →](/docs/datasets)
