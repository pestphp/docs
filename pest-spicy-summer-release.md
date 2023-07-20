---
title: Pest's Spicy Summer Release
description: Pest's Spicy Summer release is here - snapshot testing, describe blocks, type coverage, and more...
---

# Pest's Spicy Summer Release

> "Spicy Summer" is the codename assigned to Pest 2.9.

On March 20, 2023, [we proudly introduced Pest 2.0](/docs/announcing-pest2), marking it as our most significant release to date, with **more than 7 million downloads** at the time of writing. This version showcased a remarkable architectural plugin, an 80% speed improvement in parallel testing, profiling options, and numerous other features.

As we approach summer, we are thrilled to announce our upcoming release: the highly anticipated "Spicy Summer" release. This release brings an array of exciting features that will make it feel like a major version without actually being one - it's Pest v2.9.0 - so **it's just a "composer update" away from you**. Without further delay, let's dive into what we have in store for you this summer:

- **Built-in Snapshot Testing**, for testing the long-output of your code with ease
- **Describe Blocks**, for grouping tests and sharing setup and teardown logic
- **Architectural Testing++**, even more powerful architectural testing
- **Type Coverage Plugin**, for measuring the percentage of code that is covered by type declarations
- **Drift Plugin**, for automatically convert your PHPUnit tests to Pest

## Built-in Snapshot Testing

> Read the full documentation at: [pestphp.com/docs/snapshot-testing](/docs/snapshot-testing)

Snapshot testing is a testing technique that allows you to assert that the output of a function or method has not changed. It's a great way to test your codebase and ensure that your code is not changing unexpectedly.

And now, we are proud to announce that Pest will have built-in snapshot testing support. As an example, let's say your "contacts" endpoint outputs a certain HTML every time it runs. You would probably write a test like this:

```php
it('has a contact page', function () {
    $response = $this->get('/contact');

    expect($response)->toMatchSnapshot();
});
```

The first time you run this test, it will create a snapshot file - at `tests/.pest/snapshots` - with the response content. The next time you run the test, it will compare the response with the snapshot file. If the response is different, the test will fail. If the response is the same, the test will pass.

In addition, the given expectation value doesn't have to be a response; it can be anything. For example, you can snapshot an array:

```php
$array = /** Fetch array somewhere */;

expect($array)->toMatchSnapshot();
```

And of course, you can "rebuild" the snapshots at any time by using the `--update-snapshots` option:

```bash
./vendor/bin/pest --update-snapshots
```

## Describe Blocks

Since we released Pest, describe blocks has been one of the most requested features. These are fundamental to any "functional" testing framework, as they allow you to group tests and share setup and teardown logic.

```php
beforeEach(fn () => $this->user = User::factory()->create());

describe('auth', function () {
    beforeEach(fn () => $this->actingAs($this->user));

    test('cannot login when already logged in', function () {
        // ...
    });

    test('can logout', function () {
        // ...
    });
})->skip(/* Skip the entire describe block */);

describe('guest', function () {
    test('can login', function () {
        // ...
    });
    
    // ...
});
```

## Architectural Testing++

> Read the full documentation at: [pestphp.com/docs/arch-testing](/docs/arch-testing)

Pest has always been about making testing more enjoyable. Last release, we introduced architectural expectations, which allow you to test your codebase's architecture. This release, we are proud to announce that Pest improves architectural expectations by adding new ones.

```php
test('controllers')
    ->expect('App\Http\Controllers')
    ->toUseStrictTypes()
    ->toHaveSuffix('Controller') // or toHavePreffix, ...
    ->toBeReadonly() 
    ->toBeClasses() // or toBeInterfaces, toBeTraits, ...
    ->classes->not->toBeFinal() // 🌶
    ->classes->toExtendNothing() // or toExtend(Controller::class),
    ->classes->toImplementNothing() // or toImplement(ShouldQueue::class),
```

## Type Coverage Plugin

> Read the full documentation at: [pestphp.com/docs/type-coverage](/docs/type-coverage)

<img src="/assets/img/type-coverage.png" style="width: 100%;" />

As you may know, Pest offers a `--coverage` flag that allows you to generate a gorgeous coverage report on the terminal. This report shows you which lines of code are covered by your tests. This is a great way to ensure that your tests are covering all of your code.

To add to this, we are proud to announce that Pest will now have built-in type coverage support. This means that you can now see if your source code is using "types" in every possible place. For example, let's say you have a repository that has the following method:

```php
public function find($id)
{
    return User::find($id);
}
```

This method is missing a parameter type and a return type. So, if you run pest --type-coverage, you will see the following output and know that you need to add types to this method:

```bash
...
app/Models\User.php .......................................... 100%
app/Repositories/UserRepository.php .................. pa8, rt8 33%
───────────────────────────────────────────────────────────────────
Total: 91.6 %
```

In addition, just like regular coverage, you may enforce `--min` type coverage percentage. For example, if you run `--type-coverage --min=100`, you will see the following output:

```bash
  ...
  app/Models\User.php .......................................................... 100%
  app/Repositories/UserRepository.php .................................. pa8, rt8 33%
  ───────────────────────────────────────────────────────────────────────────────────
                                                                        Total: 91.6 %
   ERROR  Type coverage below expected: 91.6%. Minimum: 100.0% 
```

## Drift Plugin

> Read the full documentation at: [pestphp.com/docs/migrating-from-phpunit-guide](/docs/migrating-from-phpunit-guide)

Yes, you read that right. We are proud to announce that Pest will now have a Laravel shift-like tool called Drift. Drift will allow you to upgrade your PHPUnit tests to Pest tests in a matter of seconds.

So, if you have a test like this:

```php
<?php

namespace Tests\Unit;

use PHPUnit\Framework\TestCase;

class ExampleTest extends TestCase
{
    public function test_that_true_is_true(): void
    {
        $this->assertTrue(true);
    }
}
```

You can run `./vendor/bin/pest --drift` and Pest will automatically convert your PHPUnit test to a Pest test:

```php


test('true is true', function () {
    expect(true)->toBeTrue();
});
```

---

Thank you for reading about Pest 2.9's new features! If you're considering a testing framework for your next project, here's why you should give Pest a try: [Why Pest →](/docs/why-pest)
