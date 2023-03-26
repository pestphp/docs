---
title: Higher Order Testing
description: Although "High Order Testing" may appear to be a complex term, it is actually a technique that simplifies your tests and it is entirely optional. One of the core philosophies of Pest is to encourage users to care about the beauty and simplicity of their test suite, just as they do about their source code. Therefore, you might find this technique intriguing and choose to adopt it in certain parts of your code.
---

# Higher Order Testing

Although "High Order Testing" may appear to be a complex term, it is actually a technique that simplifies your tests and it is entirely optional. One of the core philosophies of Pest is to encourage users to care about the beauty and simplicity of their test suite, just as they do about their source code. Therefore, you might find this technique intriguing and choose to adopt it in certain parts of your code.

Let's consider an example that demonstrates how to migrate an existing test to high order testing. To illustrate, we will use a simple test.

```php
it('works', function () {
    $this->get('/')
        ->assertStatus(200);
});
```

Based on this example, you can see that the entire content of the test is chained calls made on the `$this` variable. In such cases, it is possible to eliminate the test closure entirely and chain the required methods together directly to the `it()` function.

```php
it('works')
    ->get('/')
    ->assertStatus(200);
```

The technique of removing the closure function and directly chaining the methods of the test body to the `test()` or `it()` functions is commonly referred to as "High Order Testing". This approach can significantly simplify the code of your test suite.

This technique can also be combined with the [expectation API](/docs/expectations). Let's look at a test where the expectation API is used to verify that a user was created with the correct name.

```php
it('has a name', function () {
    $user = User::create([
        'name' => 'Nuno Maduro',
    ]);

    expect($user->name)->toBe('Nuno Maduro');
});
```

If your test contains only one expectation, we can simplify it using high-order testing.

```php
it('has a name')
    ->expect(fn () => User::create(['name' => 'Nuno Maduro'])->name)
    ->toBe('Nuno Maduro');
```

It is crucial to use lazy evaluation for the expectation value by passing a closure to the `expect()` method. This ensures that the expected value is created only when the test runs and not before.

If you need to make assertions on an object that requires lazy evaluation at runtime, you can use the `defer()` method.

```php
it('creates admins')
    ->defer(fn () => $this->artisan('user:create --admin'))
    ->assertDatabaseHas('users', ['id' => 1]);
```

In the example above, the `assertDatabaseHas()` assertion method will be called on the result of the closure passed to the `defer()` method.

The principles of high-order testing can also be applied to hooks. This means that if the body of your hook consists of a sequence of methods chained to the `$this` variable, you can simply chain those methods to the hook method and omit the closure entirely.

```php
beforeEach(function () {
    $this->withoutMiddleware();
});

// Can be rewritten as...
beforeEach()->withoutMiddleware();
```

When using higher order testing, dataset values are passed to the `expect()` and `defer()` closures for convenience.

```php
it('validates emails')
    ->with(['taylor@laravel.com', 'enunomaduro@gmail.com'])
    ->expect(fn (string $email) => Validator::isValid($email))
    ->toBeTrue();
```

## Higher Order Expectations

With Higher Order Expectations, you can perform expectations directly on the `properties` or `methods` of the expectation `$value`.

For example, imagine you're testing that a user was created successfully and a variety of attributes have been stored in the database. Your test might look something like this:

```php
expect($user->name)->toBe('Nuno');
expect($user->surname)->toBe('Maduro');
expect($user->addTitle('Mr.'))->toBe('Mr. Nuno Maduro');
```

To utilize Higher Order Expectations, you can simply chain the properties and methods directly to the `expect()` function, and Pest will take care of retrieving the property value or calling the method on the `$value` under test.

Now, let's see the same test refactored to Higher Order Expectations.

```php
expect($user)
    ->name->toBe('Nuno')
    ->surname->toBe('Maduro')
    ->addTitle('Mr.')->toBe('Mr. Nuno Maduro');
```

When working with arrays, you may also access the `$value` array keys and perform expectations on them.

```php
expect(['name' => 'Nuno', 'projects' => ['Pest', 'OpenAI', 'Laravel Zero']])
    ->name->toBe('Nuno')
    ->projects->toHaveCount(3)
    ->each->toBeString();
   
expect(['Dan', 'Luke', 'Nuno'])
    ->{0}->toBe('Dan');
```

Higher Order Expectations can be used with all [Expectations](/docs/expectations), and you may even create further Higher Order Expectations within closures.

```php
expect(['name' => 'Nuno', 'projects' => ['Pest', 'OpenAI', 'Laravel Zero']])
    ->name->toBe('Nuno')
    ->projects->toHaveCount(3)
    ->sequence(
        fn ($project) => $project->toBe('Pest'),
        fn ($project) => $project->toBe('OpenAI'),
        fn ($project) => $project->toBe('Laravel Zero'),
    );
```

## Scoped Higher Order Expectations

With Scoped Higher Order Expectations, you may use the method `scoped()` and a closure to gain access and lock an expectation in to a certain level in the chain.

This is very useful for Laravel Eloquent models, where you want to check properties of a child relation.

```php
    expect($user)
    ->name->toBe('Nuno')
    ->email->toBe('enunomaduro@gmail.com')
    ->address()->scoped(fn ($address) => $address
        ->line1->toBe('1 Pest Street')
        ->city->toBe('Lisbon')
        ->country->toBe('Portugal')
    );
```

---

Although higher order testing may appear complicated, it is a technique that can significantly simplify your test suite's code. In the next section, we will discuss Pest's community video resources: [Video Resources](/docs/video-resources)
