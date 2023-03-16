---
title: Hooks
description: Hooks in Pest are similar to the steps that you might take when preparing a meal - first, you gather and prepare the ingredients, then you cook the meal, and finally, you clean up after yourself.
---

# Hooks

Hooks in Pest are similar to the steps that you might take when preparing a meal - first, you gather and prepare the ingredients, then you cook the meal, and finally, you clean up after yourself. In the same way, hooks allow you to perform specific actions before and after each test or file, such as setting up test data, initializing the test environment, or cleaning up resources after the tests are complete.

By using hooks in Pest, you can streamline your testing process, automate repetitive tasks, and ensure that your tests are executed consistently and reliably. Whether you're writing unit tests for a small project or building a complex test suite for a large application, hooks can help you save time and improve the quality of your tests.

<div class="collection-method-list" markdown="1">

- [`beforeEach()`](#beforeeach)
- [`afterEach()`](#aftereach)
- [`beforeAll()`](#beforeall)
- [`afterAll()`](#afterall)

</div>

<a name="beforeeach"></a>
## `beforeEach()`

Executes the provided closure before every test within the current file, ensuring that any necessary setup or configuration is completed before each test.

```php
beforeEach(function () {
    // prepare something before each test run
});
```

When using the `beforeEach()` hook, it's possible to initialize properties that will be shared across all tests within the current file. For example, you could use `beforeEach()` to initialize the `$repository` property before each test is run, ensuring that it's available and set up correctly.

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

Executes the provided closure after every test within the current file, allowing you to clean up any resources or state that may have been modified during testing.

```php
afterEach(function () {
    // clear testing data after each test run
});
```

So, on the example used above, if the `beforeEach()` hook is used to initialize the `$userRepository` property, the `afterEach()` hook is used to "clean" it after each test, if necessary. This ensures that the object is cleared and reset between tests, preventing any interference or unwanted behavior.

```php
//

afterEach(function () {
    $this->userRepository->reset();
});
```

By using `afterEach()` in this way, you can ensure that any necessary cleanup or tear-down actions are performed after each test, helping to keep your test environment clean and consistent.

<a name="beforeall"></a>
## `beforeAll()`

Executes the provided closure once before any tests are run within the current file, allowing you to perform any necessary setup or initialization that applies to all tests.

```php
beforeAll(function () {
    // prepare something once before all tests run
});
```

It's important to note that unlike the `beforeEach()` hook, the `$this` variable is not available in the `beforeAll()` hook. This is because the hook runs before any tests are executed, so there is no instance of the test class or object to which the variable could refer.

<a name="afterall"></a>
## `afterAll()`

Executes the provided closure once after all tests have completed within the current file, allowing you to perform any necessary cleanup or teardown tasks.

```php
afterAll(function () {
    // clean something after all tests run
});
```

Just like `beforeAll`, and unlike the `afterEach()` the `$this` variable may not be available in the `afterAll()` hook. This is because the `afterAll()` hook typically runs after all tests in the file have completed, so there may not be a test instance or object to which the variable could refer.

---

Once you've mastered using Hooks to set up preconditions and cleanup actions for your tests, the next section in the documentation, "Datasets", covers the use of datasets to run the same test with different inputs or parameters. These datasets can be used to thoroughly test your code under a variety of conditions and edge cases, and can help you identify and fix bugs that may not be immediately obvious. By using data providers or datasets, you can write more efficient and effective tests that cover a wider range of scenarios: [Datasets →](/docs/datasets)
