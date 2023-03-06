---
title: Customizing Expectations
description: ...
---

# Expectations


<a name="higher-order-expectations"></a>

## Higher Order Expectations

> **NOTE:** You cannot call methods and properties on your expectation value that conflict with methods in the `Expectation` API using Higher Order Expectations.

You may create expectations on `methods` or `properties` of the original expectation value. As an example, imagine you're testing that a `User` can be created within your system. You might want to test that a variety of attributes have been stored correctly:

```php
expect($user->first_name)->toEqual('Nuno');
expect($user->last_name)->toEqual('Maduro');
expect($user->withTitle('Mr'))->toEqual('Mr Nuno Maduro');
```

With higher order expectations, you can refactor that test to:

```php
expect($user)
    ->first_name->toEqual('Nuno')
    ->last_name->toEqual('Maduro')
    ->withTitle('Mr')->toEqual('Mr Nuno Maduro');
```

You may also access `array` keys to perform expectations on them:

```php
expect(['name' => 'Nuno', 'companies' => ['Pest', 'Laravel']])
    ->name->toEqual('Nuno')
    ->companies->toHaveCount(2)->each->toBeString
```

Pest takes care of retrieving the property or calling the method on the value under test.

Higher order expectations can be used with all of [Pest's expectations](#available-expectations). Which means you can create tests that are both powerful and elegant - even creating further higher order expectations within [`each()`](#expect-each) and [`sequence()`](#expect-sequence) closures:

```php
expect($user)
    ->posts
    ->not->toBeEmpty
    ->toHaveCount(2)
    ->sequence(
        fn ($post) => $post->title->toEqual('My first post!'),
        fn ($post) => $post->title->toEqual('My second post')
    );
```

Your Higher Order Expectations can reach as deep into an object or associative array as you like. Once you perform one
or more of [Pest's expectations](#available-expectations), the expectation's scope will reset to the initial value again:

```php
expect($user)
    ->companies->first()->owner->toBeInstanceOf(User::class)->not->toEqual($user)
    ->name->toEqual('Nuno');
```