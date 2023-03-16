---
title: Mocking
description: Installing Pest PHP Testing Framework is a simple process that can be completed in just a few steps.
---

# Mocking

> **Requirements:** [Mockery 1.0+](https://github.com/mockery/mockery/)

During application testing, you may want to "mock" specific components to prevent them from being executed during a particular test. For instance, if you encounter an API that initiates a payment, you may want to locally mock the API Client to prevent the actual request from being made. This approach enables you to focus solely on testing your application or library without the need to deal with the actual API functioning behind the scenes.

If your use case requires mocking features in your application or library, you will need to install a mocking library. Our personal recommendation is [Mockery](https://github.com/mockery/mockery/), but you are free to choose any other library that suits your needs.

To begin using Mockery, you will first need to require it through Composer.

```bash
composer require mockery/mockery --dev
```

While comprehensive documentation for Mockery can be found on its [dedicated website](https://docs.mockery.io), this section will focus on the most commonly encountered use cases.

## Method Expectations

Mock objects are essential for isolating the code being tested and simulating specific behaviors or conditions. To indicate that a test mock should anticipate a call to a method with a given name, we use the `shouldReceive` method, as demonstrated in the code snippet below.

```php
use App\Repositories\BookRepository;
use Mockery;

it('may buy a book', function () {
    $client = Mockery::mock(PaymentClient::class);
    $client->shouldReceive('post');

    $books = new BookRepository($client);
    $books->buy(); // The API is not actually reached as `$client->post()` has been mocked...
});

```

It is possible to specify multiple method calls using the same syntax.

```php
$client->shouldReceive('post');
$client->shouldReceive('delete');
```

## Argument Expectations

In order to make our expectations for a method more specific, we can use constraints to limit the expected argument list for that method call. This can be done by utilizing the `with()` method, as demonstrated in the following example.

```php
$client->shouldReceive('post')
    ->with($firstArgument, $secondArgument);
```

In order to increase the flexibility of argument matching, Mockery provides built-in matcher classes that can be used in place of specific values. For example, instead of using specific values, we can use `Mockery::any()` to match any argument passed to that position in the `with()` parameter list.

```php
$client->shouldReceive('post')
    ->with($firstArgument, Mockery::any());
```

It is important to note that expectations set up using `shouldReceive()` and `with()` only apply to the specified method when it is called with the exact arguments that were specified.

```php
$client->shouldReceive('post')->with(1);

$client->post(2); // test fails, throws a `NoMatchingExpectationException`
```

In certain cases, it may be more appropriate to use a closure to match all passed arguments simultaneously, rather than relying on built-in matchers for each individual argument. The `withArgs()` method accepts a closure that receives all of the arguments passed to the expected method call. As a result, this expectation will only be applied to method calls in which the passed arguments cause the closure to evaluate to true.

```php
$client->shouldReceive('post')->withArgs(function ($arg) {
    return $arg === 1;
});

$client->post(1); // test passes, matches the expectation
$client->post(2); // test fails, throws a `NoMatchingExpectationException`
```

## Return Values

When working with mock objects, we can use the `andReturn()` method to inform Mockery of the desired return values for the expected method calls.

```php
$client->shouldReceive('post')->andReturn('post response');
```

We can establish expectations for multiple return values by using a sequence of return values.

```php
$client->shouldReceive('post')->andReturn(1);
$client->shouldReceive('post')->andReturn(2);

$client->post(); // int(1)
$client->post(); // int(2)
```

Sometimes, we may need to calculate the return results of method calls based on the arguments passed to the method. This can be accomplished using the `andReturnUsing()` method, which accepts one or more closures.

```php
$mock->shouldReceive('post')
    ->andReturnUsing(
        fn () => 1,
        fn () => 2,
    );
```

We can instruct the methods of mock objects to throw exceptions if needed.

```php
$client->shouldReceive('post')
    ->andThrow(new Exception);
    // andThrow
```

## Method Call "Count" Expectations

Along with specifying expected arguments and return values for method calls, we can also set expectations for how many times a particular method should be invoked.

```php
$mock->shouldReceive('post')->once();
$mock->shouldReceive('put')->twice();
$mock->shouldReceive('delete')->times(3);
// ...
```

To specify the minimum number of method calls to Mockery, we can use the `atLeast()` method.

```php
$mock->shouldReceive('delete')->atLeast()->times(3);
```

The `atMost()` method in Mockery allows us to indicate the upper limit of how many times a method can be called.

```php
$mock->shouldReceive('delete')->atMost()->times(3);
```

---

The primary objective of this section is to provide you with an introduction to Mockery, the mocking library we prefer. However, for a more comprehensive understanding of Mockery, we suggest referring to its documentation website at [docs.mockery.io](https://docs.mockery.io). Let us now explore Pest's plugins and discover how they can enhance your Pest experience: [Plugins](/docs/plugins)
