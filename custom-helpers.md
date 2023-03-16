---
title: Custom Helpers
description: If you're transitioning to a functional approach for writing tests, you may wonder where to put your helpers that used to be protected or private methods in your test classes. This chapter aims to clarify that, in line with other functional programming languages, helpers can exist outside of conventional classes.
---

# Custom Helpers

If you're transitioning to a functional approach for writing tests, you may wonder where to put your helpers that used to be protected or private methods in your test classes. This chapter aims to clarify that, in line with other functional programming languages, helpers can exist outside of conventional classes.

However, it's important to note that if your helper involves creating a custom expectation, you should make a [custom expectation](/docs/custom-expectations) instead. Otherwise, creating a helper is a straightforward process - you can simply create a function.

If your helper is specific to your test file, you may create the helper in the test file directly. If you use the `test` function without passing any arguments, you can access the `$this` variable inside the closure.

```php
use App\Models\User;

function asAdmin(): User
{
    $user = User::factory()->create([
        'admin' => true,
    ]);

    return test()->actingAs($user);
}

it('can manage users', function () {
    asAdmin()->get('/users')->assertOk();
})
```

If your test helpers are utilized throughout your test suite, you have the alternative of keeping them in either `tests/Pest.php` or `tests/Helpers.php`, which will be automatically loaded by Pest.

```php
use App\Clients\PaymentClient;
use Mockery;

// tests/Pest.php or tests/Helpers.php
function mockPayments(): object
{
    $client = Mockery::mock(PaymentClient::class);

    //

    return $client;
}

// tests/Features/PaymentsTest.php
it('may buy a book', function () {
    $client = mockPayments();

    //
})
```

As previously indicated in the [Configuring Tests](/docs/configuring-tests) section, you have the option of generating protected methods in your base test class and subsequently accessing them in your test cases using the `$this` variable.

```php
use App\Clients\PaymentClient;
use PHPUnit\Framework\TestCase as BaseTestCase;
use Mockery;

// tests/TestCase.php
class TestCase extends BaseTestCase
{
    public function mockPayments(): void
    {
        $client = Mockery::mock(PaymentClient::class);

        //

        return $client;
    }
}

// tests/Pest.php
uses(TestCase::class)->in('Features');

// tests/Features/PaymentsTest.php
it('may buy a book', function () {
    $client = $this->mockPayments();

    //
})
```

---

In this section, we explored creating custom helpers. However, at times, you might want to generate a custom expectation. We will delve into that topic in the next chapter: [Custom Expectations](/docs/custom-expectations)