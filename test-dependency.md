---
title: Test Dependency
description: Test Dependency
---

# Test Dependency

- [Overview](#overview)
- [Returning values](#returning-values)
- [Multiple dependencies](#multiple-dependencies)

<a name="overview"></a>

https://subscription.packtpub.com/book/application-development/9781782169581/1/ch01lvl1sec17/using-test-dependencies-advanced

## Overview

Sometimes your tests depends on each other.

Pest will understand that you only want to run the Child test if the Parent test has passed.

```php
test('Parent', function () {
    expect(true)->toBeTrue();
});

test('Child', function () {
    expect(false)->toBeFalse();
})->depends('Parent');
```

Resulting in:

```plain
   PASS  Tests\Unit\ExampleTest
  ✓ Parent
  ✓ Child

  Tests:  2 passed
```

However, If the Parent test fails, the Child test will be skipped.

```php
test('Parent', function () {
    expect(0)->toBe(1);
});

test('Child', function () {
    expect(false)->toBeFalse();
})->depends('Parent');
```

The example above results in:

```plain
   FAIL  Tests\Unit\ExampleTest
  ⨯ Parent
  ! Child → This test depends on "P\Tests\Unit\ExampleTest::Parent" which does not exist.
```

Keep in mind that using the `it()` function will append "it" to your test name. 

This means you must include "it" when referencing this test in `depends()`.

```php
it('is the Parent', function () {
    expect(true)->toBeTrue();
});

test('Child', function () {
    expect(false)->toBeFalse();
})->depends('it is the Parent');
```

Results is:

```plain
   PASS  Tests\Unit\ExampleTest
  ✓ it is the Parent
  ✓ Child

  Tests:  2 passed
```

<a name="returning-values"></a>

## Returning values

For the next example, 

```php
//Class for demonstration
class Greeting
{
    public string $name = '';
    
    public function sayHello()
    {
        return "Hello {$this->name}!";
    }
}

//The first test asserts that class has the property name

test('#1 initial', function () {
    $greeting = new Greeting();

    expect($greeting)->toHaveProperty('name');

    $this->assertTrue(property_exists($greeting, 'name'));

    return $greeting;
});

//Then, depending on the first test, it asserts that property name is empty

test('name is empty', function (Greeting $greeting) {
    expect($greeting)->name->toBe('');

    return $greeting;
})->depends('#1 initial');

//Next, depending on the first test, it asserts that it can say hello to a given name

it('can say hello', function (Greeting $greeting) {
    $greeting->name = 'Dan';
    
    expect($greeting)->sayHello()
        ->toBe('Hello Dan!');

    return $greeting;
})->depends('#1 initial');

//Finally, depending on "it can say hello", it asserts that sayHello works with two names.

test('say hello with two names', function (Greeting $greeting) {
    $doubleGreeting = clone $greeting;
    
    $doubleGreeting->name .= ' and Nuno';
    
    expect($doubleGreeting)->sayHello()
        ->toBe('Hello Dan and Nuno!');

    return $doubleGreeting;
})->depends('it can say hello');
```

Resulting in:

```plain
   PASS  Tests\Unit\ExampleTest
  ✓ #1 initial
  ✓ name is empty
  ✓ it can say hello
  ✓ say hello with two names

  Tests:  4 passed
```

<a name="multiple-dependencies"></a>

## Multiple dependencies

You may also add multiple dependencies to one test.

To demonstrate this concept, let's add a new test to the [Returning values example](#returning-values).

The new test depends on the two previous tests **#1 initial** and  **say hello with two names**. Both tests must pass and the value returned by each test will be accessible in the new test via function parameters.

```php
test('say hello with uppercase', function (Greeting $greeting, Greeting $doubleGreeting) {
    //Value comes from "#1 initial" test
    expect(strtoupper($greeting->name))->toBe('DAN');

    //Value comes from "say hello with two names" test
    expect(strtoupper($doubleGreeting->sayHello()))
        ->toBe('HELLO DAN AND NUNO!');
})->depends('#1 initial', 'say hello with two names');
```

Resulting in:

```plain
   PASS  Tests\Unit\ExampleTest
  ✓ #1 initial
  ✓ name is empty
  ✓ it can say hello
  ✓ say hello with two names
  ✓ say hello with uppercase

  Tests:  5 passed
```

Next section: [Skipping Tests →](/docs/skipping-tests)
