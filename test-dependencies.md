---
title: Test Dependencies
description: Sometimes, tests typically require certain preconditions or events to occur prior to their execution, or else they will not succeed. As an illustration, you can only verify that users are able to modify their accounts if you have first verified that an account can be established.
---

# Test Dependency

Sometimes, tests typically require certain preconditions or events to occur prior to their execution, or else they will not succeed. As an illustration, you can only verify that users are able to modify their accounts if you have first verified that an account can be established.

To address this issue, Pest offers the `depends()` method, which allows a "Child" test to specify that it depends on one or more "Parent" tests.

```php
test('parent', function () {
    expect(true)->toBeTrue();

    $this->assertTrue(true);
});

test('child', function () {
    expect(false)->toBeFalse();
})->depends('parent');
```

As illustrated earlier, the `child` test is reliant on the `parent` test. If the `parent` test successfully completes, the `child` test will then be triggered.

```php
   PASS  Tests\Unit\ExampleTest
 ✓ Parent
 ✓ Child

   Tests:  2 passed
```

Nonetheless, if the `parent` test fails, the `child` test will be bypassed, accompanied by a message that is easy to understand for the user.

```php
test('parent', function () {
    expect(true)->toBeFalse();
});

test('child', function () {
    expect(false)->toBeFalse();
})->depends('parent');
```

The example above results in:

```php
   FAIL  Tests\Unit\ExampleTest
  ⨯ Parent
  ! Child → This test depends on "Tests\Unit\ExampleTest::Parent" which does not exist.
```

It is important to remember that the `it()` function adds the suffix "it" to the test name by default. Thus, when referencing the test name in depends(), you should include the "it " suffix.

```php
it('is the parent', function () {
    expect(true)->toBeTrue();
});

test('child', function () {
    expect(false)->toBeFalse();
})->depends('it is the parent');
```

Results is:

```php
   PASS  Tests\Unit\ExampleTest
  ✓ it is the parent
  ✓ child

  Tests:  2 passed
```

Parent tests can provide return values that can be accessed as arguments in the `child` test.

```php
test('parent', function () {
    expect(true)->toBeTrue();

    return 'coming from parent';
});

test('child', function ($parentValue) {
    var_dump($parentValue); // coming from parent

    expect($parentValue)->toBe('coming from parent');
})->depends('parent');
```

It is also possible to add multiple dependencies to a test. However, all parent tests must pass, and the values returned by each test will be available as function parameters, in the order of their dependencies.

```php
test('a', function () {
    expect(true)->toBeTrue();

    return 'a';
});

test('b', function () {
    expect(true)->toBeTrue();

    return 'b';
});

test('c', function () {
    expect(true)->toBeTrue();

    return 'c';
});

test('d', function ($testA, $testC, $testB) {
    var_dump($testA); // a
    var_dump($testB); // b
    var_dump($testC); // c
})->depends('a', 'b', 'c');
```

---

While test dependencies are uncommon, they can be useful for optimizing your tests and minimizing the need to recreate resources repeatedly. In the next chapter, we will explore the concept of "High Order Testing": [High Order Testing](/docs/high-order-testing)
