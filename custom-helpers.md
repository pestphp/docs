---
title: Custom Helpers
description: If you're transitioning to a functional approach for writing tests, you may wonder where to put your helpers that used to be protected or private methods in your test classes. When using Pest, these helper methods should be converted to simple functions.
---

# Custom Helpers

If you're transitioning to a functional approach for writing tests, you may wonder where to put your helpers that used to be protected or private methods in your test classes. When using Pest, these helper methods should be converted to simple functions.

For example, if your helper is specific to a certain test file, you may create the helper in the test file directly. Within your helper, you may invoke the `test()` function to access the test class instance that would normally be available via `$this`.

```php
use App\Models\User;
use Tests\TestCase;

function asAdmin(): TestCase
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

> **Note:** If your helper creates a custom expectation, you should write a dedicated [custom expectation](/docs/custom-expectations) instead.

If your test helpers are utilized throughout your test suite, you may define them within the `tests/Pest.php` or `tests/Helpers.php` files. Alternatively, you can create a `tests/Helpers` directory to house your own helper files. All of these options will be automatically loaded by Pest.

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

As an alternative to defining helper methods as functions, you may define protected methods in your base test class and subsequently access them in your test cases using the `$this` variable.

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

In this section, we explored creating custom helpers. Digging deeper, you may even want to generate a custom expectation. Let's jump into that topic in the next chapter: [Custom Expectations](/docs/custom-expectations)
