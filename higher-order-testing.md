---
title: Higher Order Testing
description: Although "High Order Testing" may appear to be a complex term, it is actually a technique that simplifies your tests, and it is entirely optional. One of the core philosophies of Pest is to encourage users to care about the beauty and simplicity of their test suite, just as they do about their source code. Therefore, you might find this technique intriguing and choose to adopt it in certain parts of your code.
---

# Higher Order Testing

Although "High Order Testing" may appear to be a complex term, it is actually a technique that simplifies your tests, and it is entirely optional. One of the core philosophies of Pest is to encourage users to care about the beauty and simplicity of their test suite, just as they do about their source code. Therefore, you might find this technique intriguing and choose to adopt it in certain parts of your code.

Let us consider an example that demonstrates how to migrate an existing test to high order testing. To illustrate, we will use a simple test.

```php
it('works', function () {
    $this->get('/')
        ->assertStatus(200);
});
```

Based on this example, it can be inferred that the content of the test is entirely determined by the chained calls made on the `$this` variable. In such cases, it is possible to eliminate the closure function and chain the required methods together directly.

```php
it('works')
    ->get('/')
    ->assertStatus(200);
```

The technique of removing the closure function and directly chaining the methods of the test body to the test or it function is commonly referred to as "High Order Testing". This approach can significantly simplify the code of your test suite.

This technique can also be combined with the [Expectation API](/docs/expectations). Here is an example where we use the Expectation API to verify whether a user was created with the correct name.

```php
it('has a name', function () {
    $user = User::create([
        'name' => 'Nuno Maduro',
    ]);

    expect($user->name)->toBe('Nuno Maduro');
});
```

If your test contains only one expectation, you may want to simplify it using high-order testing.

```php
it('has a name')
    ->expect(fn () => User::create(['name' => 'Nuno Maduro'])->name)
    ->toBe('Nuno Maduro');
```

It is crucial to use lazy evaluation for the expectation value by passing a closure. This approach ensures that the expected value is created only when the test runs and not before that.

If you need to call assertions on an object that requires lazy evaluation at runtime, you can use the `defer` method for such cases.

```php
it('create admins')
    ->defer(fn () => $this->artisan('user:create --admin'))
    ->assertDatabaseHas('users', ['id' => 1]);
```

In this case, the `assertDatabaseHas` assertion will be performed on the result of the closure passed to the `defer` method.

The principles of high-order testing can also be applied to hooks. This means that if the body of your hook consists of a sequence of methods chained to the $this variable, you can simply chain those methods and omit the closure.

```php
beforeEach(function () {
    $this->withoutMiddleware();
});

//
```

By definition, the same hook can be written using high-order testing.

```php
beforeEach()->withoutMiddleware();

//
```

Finally, you may wonder where to provide dataset arguments when no closure is present. In such cases, dataset arguments are passed to the expect and defer closures, and you can use them to access the values of the dataset.

```php
it('validates emails')
    ->with(['taylor@laravel.com', 'enunomaduro@gmail.com'])
    ->expect(fn (string $email) => Validator::isValid($email))
    ->toBeTrue();
```

---

Although high-order testing may appear complicated, it is a technique that can significantly simplify your test suite's code. In the next section, we will discuss Pest's Support Policy: [Support Policy](/docs/support-policy)

