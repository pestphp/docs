---
title: Mocking
description: When testing your applications, you may wish to "mock" specific classes to prevent them from being invoked during a particular test — for instance, mocking a payment API client locally so that no actual payment is made.
---

# Mocking

> **Requirements:** [Mockery 1.0+](https://github.com/mockery/mockery/)

When testing your applications, you may wish to "mock" specific classes to prevent them from actually being invoked during a particular test. For instance, if your application interacts with an API that initiates a payment, you likely want to "mock" the API client locally so that the actual payment is never made.

Before getting started, you will need to install a mocking library. We recommend [Mockery](https://github.com/mockery/mockery/), but you are free to choose any other library that suits your needs.

To begin using Mockery, you may require it using the Composer package manager:

```bash
composer require mockery/mockery --dev
```

Comprehensive documentation for Mockery can be found on the [Mockery website](https://docs.mockery.io); this section will focus on the most common use cases for mocking.

## Method Expectations

Mock objects are essential for isolating the code under test and simulating specific behaviors or conditions from other parts of your application. Once you have created a mock using the `Mockery::mock()` method, you may indicate that you expect a certain method to be invoked by calling the `shouldReceive()` method:

```php
use App\Repositories\BookRepository;
use Mockery;

it('may buy a book', function () {
    $client = Mockery::mock(PaymentClient::class);
    $client->shouldReceive('post');

    $books = new BookRepository($client);
    $books->buy(); // The API is not actually invoked since `$client->post()` has been mocked...
});

```

You may mock multiple method calls using the same syntax shown above:

```php
$client->shouldReceive('post');
$client->shouldReceive('delete');
```

## Argument Expectations

To make your expectations for a method more specific, you may use constraints to limit the expected argument list for a method call. This is accomplished with the `with()` method, as demonstrated in the following example:

```php
$client->shouldReceive('post')
    ->with($firstArgument, $secondArgument);
```

To increase the flexibility of argument matching, Mockery provides built-in matcher classes that may be used in place of specific values. For example, instead of passing specific values, you may use `Mockery::any()` to match any argument:

```php
$client->shouldReceive('post')
    ->with($firstArgument, Mockery::any());
```

It is important to note that expectations defined using `shouldReceive()` and `with()` only apply when the method is invoked with the exact arguments you expected. Otherwise, Mockery will throw an exception:

```php
$client->shouldReceive('post')->with(1);

$client->post(2); // fails, throws a `NoMatchingExpectationException`
```

Sometimes you may wish to match all passed arguments at once using a closure, rather than relying on built-in matchers for each individual argument. The `withArgs()` method accepts a closure that receives all of the arguments passed to the expected method call. As a result, this expectation will only apply to method calls in which the passed arguments cause the closure to evaluate to true:

```php
$client->shouldReceive('post')->withArgs(function ($arg) {
    return $arg === 1;
});

$client->post(1); // passes, matches the expectation
$client->post(2); // fails, throws a `NoMatchingExpectationException`
```

## Return Values

When working with mock objects, you may use the `andReturn()` method to tell Mockery what to return from the mocked methods:

```php
$client->shouldReceive('post')->andReturn('post response');
```

You may define a sequence of return values by passing multiple values to the `andReturn()` method:

```php
$client->shouldReceive('post')->andReturn(1, 2);

$client->post(); // int(1)
$client->post(); // int(2)
```

Sometimes you may need to calculate the return value of a method call based on the arguments passed to it. This is accomplished with the `andReturnUsing()` method, which accepts one or more closures:

```php
$mock->shouldReceive('post')
    ->andReturnUsing(
        fn () => 1,
        fn () => 2,
    );
```

In addition, you may instruct mocked methods to throw exceptions:

```php
$client->shouldReceive('post')->andThrow(new Exception);
```

## Method Call "Count" Expectations

Along with specifying expected arguments and return values for method calls, you may also set expectations for how many times a particular method should be invoked:

```php
$mock->shouldReceive('post')->once();
$mock->shouldReceive('put')->twice();
$mock->shouldReceive('delete')->times(3);
// ...
```

To specify a minimum number of times a method should be called, you may use the `atLeast()` method:

```php
$mock->shouldReceive('delete')->atLeast()->times(3);
```

Alternatively, Mockery's `atMost()` method allows you to specify the maximum number of times a method may be called:

```php
$mock->shouldReceive('delete')->atMost()->times(3);
```

---

The goal of this section is to give you an introduction to Mockery, the mocking library we prefer. For a more comprehensive understanding, however, we suggest checking out its [official documentation](https://docs.mockery.io). Next, let's explore how snapshot testing lets you assert against large or complex output without writing it all out by hand: [Snapshot Testing](/docs/snapshot-testing)
