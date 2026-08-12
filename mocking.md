---
title: Mocking
description: When testing your applications, you may wish to "mock" specific classes to prevent them from being invoked during a particular test — for instance, mocking a payment API client locally so that no actual payment is made.
---

# Mocking

When testing your applications, you may wish to "mock" specific classes to prevent them from actually being invoked during a particular test. For instance, if your application interacts with an API that initiates a payment, you likely want to "mock" the API client locally so that the actual payment is never made.

Before getting started, you will need to install a mocking library. We recommend [Double](https://github.com/jasonmccreary/double), but you are free to choose any other library that suits your needs.

To get started, you may install Double using the Composer package manager:

```bash
composer require jasonmccreary/double --dev
```

You should also register Double's `VerifiesDoubles` trait in your `Pest.php` configuration file, so that every expectation you set is verified automatically at the end of each test:

```php
// tests/Pest.php
use JMac\Testing\Integrations\PHPUnit\VerifiesDoubles;

pest()->use(VerifiesDoubles::class);
```

Comprehensive documentation for Double can be found on the [Double website](https://testdoublephp.com); this section will focus on the most common use cases for mocking.

## Method Expectations

Doubles are essential for isolating the code under test and simulating specific behaviors or conditions from other parts of your application. Once you have created a double using the `Double::for()` method, you may indicate that you expect a certain method to be invoked by calling the `expects()` method:

```php
use App\Repositories\BookRepository;
use JMac\Testing\Double;

it('may buy a book', function () {
    $client = Double::for(PaymentClient::class);
    $client->expects('post');

    $books = new BookRepository($client);
    $books->buy(); // The API is not actually invoked since `$client->post()` has been mocked...
});
```

You may double multiple method calls using the same syntax shown above:

```php
$client->expects('post');
$client->expects('delete');
```

## Argument Expectations

To make your expectations for a method more specific, you may use constraints to limit the expected argument list for a method call. This is accomplished with the `with()` method, as demonstrated in the following example:

```php
$client->expects('post')
    ->with($firstArgument, $secondArgument);
```

To increase the flexibility of argument matching, Double provides built-in matcher classes that may be used in place of specific values. For example, instead of passing specific values, you may use `Argument::any()` to match any argument:

```php
use JMac\Testing\Matching\Argument;

$client->expects('post')
    ->with($firstArgument, Argument::any());
```

It is important to note that expectations defined using `expects()` and `with()` only apply when the method is invoked with the exact arguments you expected. When an expectation isn't met, Double fails the test:

```php
$client->expects('post')->with(1);

$client->post(2); // fails the test, since `post` was never called with the expected arguments
```

Double also ships with matchers for looser argument constraints. For example, you may constrain an argument to a particular type using `Argument::type()`, `Argument::contains()`, or to anything satisfying a callback using `Argument::satisfies()`:

```php
$client->expects('save')->with(Argument::type(Post::class));
$client->expects('shuffle')->with(Argument::contains(1));
$client->expects('find')->with(Argument::satisfies(function ($arg) {
    return $arg === 1;
}));
```

Double ships with several other matchers for common needs, such as matching a regular expression. You may find the complete list in Double's [argument matching documentation](https://testdoublephp.com/argument-matching).

## Return Values

When working with doubles, you may use the `returns()` method to tell Double what to return from the doubled methods:

```php
$client->expects('post')->returns('post response');
```

You may define a sequence of return values by passing multiple values to the `returns()` method:

```php
$client->allows('post')->returns(1, 2);

$client->post(); // int(1)
$client->post(); // int(2)
```

Sometimes you may need to calculate the return value of a method call based on the arguments passed to it. This is accomplished with the `resolves()` method, which accepts a closure that receives the call's arguments:

```php
$double->expects('post')
    ->resolves(fn ($payload) => $payload['status']);
```

In addition, you may instruct doubled methods to throw exceptions:

```php
$client->expects('post')->throws(new Exception);
```

## Method Call "Count" Expectations

Along with specifying expected arguments and return values for method calls, you may also set expectations for how many times a particular method should be invoked:

```php
$double->expects('put')->times(2);
$double->expects('delete')->times(3);
// ...
```

To specify a minimum or maximum number of times a method should be called, you may pass the `minimum` or `maximum` argument to the `times()` method:

```php
$double->expects('put')->times(minimum: 3);
$double->expects('delete')->times(maximum: 3);
```

You may pass both `minimum` and `maximum` arguments to the `times()` method to specific a range of times a method may be called:

```php
$double->expects('save')->times(1, 3);
```

---

The goal of this section is to give you an introduction to Double, the mocking library we prefer. For a more comprehensive understanding, however, we suggest checking out its [official documentation](https://testdoublephp.com). Next, let's explore how snapshot testing lets you assert against large or complex output without writing it all out by hand: [Snapshot Testing](/docs/snapshot-testing)
