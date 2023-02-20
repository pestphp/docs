---
title: Writing Tests
description: Writing Tests
---

# Writing Tests

As you begin writing tests for your project, it's important to consider how to create and organize your test files effectively. Typically, test files are suffixed with `Test.php`, such as `ExampleTest.php`.

Let's explore a straightforward example. Imagine that your project features a global function called `sum`, which adds two numbers together. To test this function, you would create a `Tests\Unit\SumTest.php` file with the following code:

```php
test('sum', function () {
   $result = sum(1, 2);

   expect($result)->toBe(3);
});
```

After writing your test code, it's time to run your tests using Pest. When you execute the command `./vendor/bin/pest`, Pest will display a message indicating whether your tests passed or failed.

```shell
   PASS  Tests\Unit\SumTest.php
 ✓ sum

   Tests:  1 passed
```

In addition to the test function, Pest provides the convenient `it` function that prefixes the description with the word "it". This function can be used in place of the test function and can help make your tests more readable.

```php
it('performs sums', function () {
   $result = sum(1, 2);

   expect($result)->toBe(3);
});
```

In this case, when you run the command `./vendor/bin/pest`, the output will include the description "it performs sums", along with the result of the test:

```shell
   PASS  Tests\Unit\SumTest.php
 ✓ it performs sums

   Tests:  1 passed
```

## Expectation API

As you may have noticed in our previous examples, we made use of Pest's expectation API to perform assertions in our test code. The expect function is a core part of the expectation API and is used to assert that certain conditions are met.

For instance, in our previous example, we used `expect($result)->toBe(3)` to ensure that the value of `$result` is equal to `5`. Pest's expectation API provides a variety of other assertion functions that you can use to test the behavior of your code, such as `toBeTrue`, `toBeFalse`, and `toContain`.

By using the expectation API, you can write concise and readable assertions that make it clear what your code is doing and how it should behave. In the next section, we will cover some of the most commonly used assertion functions in Pest's expectation API.

You can find the full documentation for Pest's expectation API, including all available functions and their usage, on the [next chapter](/docs/expectations).

## Assertion API

While Pest's expectation API provides a convenient way to perform assertions, it's not the only option available. You can also use PHPUnit's assertion API, which is supported by Pest. This can be useful if you're already familiar with PHPUnit's assertion API or if you need to perform more complex assertions that aren't available in Pest's expectation API.

```php
test('sum', function () {
   $result = sum(1, 2);

   $this->assertSame(3, $result); // Same as expect($result)->toBe(3)
});
```

You can find the full documentation for PHPUnit's assertion API in the PHPUnit's website: [docs.phpunit.de/en/10.0/assertions.html](https://docs.phpunit.de/en/10.0/assertions.html)

---

Continue to our next section, for more information on how to use the Expectation API: [Expectations →](/docs/expectations)