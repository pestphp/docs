---
title: Higher Order Testing
description: An optional, elegant technique for simplifying your tests by chaining methods directly onto it() and test(), keeping your test suite as beautiful as your source code.
---

# Higher Order Testing

Although "Higher Order Testing" may sound like a complex term, it is a technique that simplifies your tests, and it is entirely optional. One of the core philosophies of Pest is to encourage you to care about the beauty and simplicity of your test suite, just as you do about your source code. As a result, you may find this technique appealing and choose to adopt it in certain parts of your code.

Let's consider an example that demonstrates how to migrate an existing test to higher order testing. To illustrate, we will start with a simple test:

```php
it('works', function () {
    $this->get('/')
        ->assertStatus(200);
});
```

As you can see, the entire content of the test is a series of chained calls made on the `$this` variable. In such cases, you may eliminate the test closure entirely and chain the required methods directly onto the `it()` function:

```php
it('works')
    ->get('/')
    ->assertStatus(200);
```

The technique of removing the closure and chaining the methods of the test body directly onto the `test()` or `it()` functions is commonly referred to as "Higher Order Testing". This approach can significantly simplify the code of your test suite.

This technique may also be combined with the [expectation API](/docs/expectations). Let's look at a test where the expectation API is used to verify that a user was created with the correct name:

```php
it('has a name', function () {
    $user = User::create([
        'name' => 'Nuno Maduro',
    ]);

    expect($user->name)->toBe('Nuno Maduro');
});
```

If your test contains only one expectation, you may simplify it using higher order testing:

```php
it('has a name')
    ->expect(fn () => User::create(['name' => 'Nuno Maduro'])->name)
    ->toBe('Nuno Maduro');
```

It is crucial to use lazy evaluation for the expectation value by passing a closure to the `expect()` method. This ensures that the expected value is created only when the test runs, and not before.

If you need to make assertions on an object that requires lazy evaluation at runtime, you may use the `defer()` method:

```php
it('creates admins')
    ->defer(fn () => $this->artisan('user:create --admin'))
    ->assertDatabaseHas('users', ['id' => 1]);
```

In this example, the `assertDatabaseHas()` assertion method will be called on the result of the closure passed to the `defer()` method.

The principles of higher order testing may also be applied to hooks. In other words, if the body of your hook consists of a sequence of methods chained to the `$this` variable, you may chain those methods directly onto the hook method and omit the closure entirely:

```php
beforeEach(function () {
    $this->withoutMiddleware();
});

// Can be rewritten as...
beforeEach()->withoutMiddleware();
```

When using higher order testing, dataset values are passed to the `expect()` and `defer()` closures for your convenience:

```php
it('validates emails')
    ->with(['taylor@laravel.com', 'enunomaduro@gmail.com'])
    ->expect(fn (string $email) => Validator::isValid($email))
    ->toBeTrue();
```

## Higher Order Expectations

With Higher Order Expectations, you may perform expectations directly on the properties or methods of the expectation `$value`.

For example, imagine you are testing that a user was created successfully and that a variety of attributes have been stored in the database. Your test might look something like this:

```php
expect($user->name)->toBe('Nuno');
expect($user->surname)->toBe('Maduro');
expect($user->addTitle('Mr.'))->toBe('Mr. Nuno Maduro');
```

To take advantage of Higher Order Expectations, you may chain the properties and methods directly onto the `expect()` function, and Pest will retrieve the property value or call the method on the `$value` under test for you.

Now, let's see the same test refactored to Higher Order Expectations:

```php
expect($user)
    ->name->toBe('Nuno')
    ->surname->toBe('Maduro')
    ->addTitle('Mr.')->toBe('Mr. Nuno Maduro');
```

When working with arrays, you may also access the `$value` array keys and perform expectations on them:

```php
expect(['name' => 'Nuno', 'projects' => ['Pest', 'OpenAI', 'Laravel Zero']])
    ->name->toBe('Nuno')
    ->projects->toHaveCount(3)
    ->each->toBeString();
   
expect(['Dan', 'Luke', 'Nuno'])
    ->{0}->toBe('Dan');
```

Higher Order Expectations may be used with all [Expectations](/docs/expectations), and you may even create further Higher Order Expectations within closures:

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

With Scoped Higher Order Expectations, you may use the `scoped()` method and a closure to gain access to and lock an expectation into a certain level in the chain.

This is helpful for Laravel Eloquent models, where you want to check the properties of a child relation:

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

Although higher order testing may appear complicated at first, it is a technique that can significantly simplify your test suite's code. In the next section, we will discuss Pest's community video resources: [Video Resources](/docs/video-resources)
