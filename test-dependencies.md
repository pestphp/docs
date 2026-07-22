---
title: Test Dependencies
description: Sometimes a test requires certain preconditions or events to occur before it runs, or else it will not succeed — Pest lets one test declare that it depends on another.
---

# Test Dependencies

Sometimes, tests require certain preconditions or events to occur prior to their execution, or else they will not succeed. For example, you may only be able to verify that users are able to modify their accounts once you have first verified that an account can be established.

To address this, Pest offers the `depends()` method, which allows a "Child" test to specify that it depends on one or more "Parent" tests:

```php
test('parent', function () {
    expect(true)->toBeTrue();
});

test('child', function () {
    expect(false)->toBeFalse();
})->depends('parent');
```

In this example, the `child` test will be triggered once the `parent` test has successfully completed:

<div class="terminal not-prose"><div class="l">&nbsp;&nbsp;<span class="chip pass">PASS</span>&nbsp;&nbsp;<span class="white">Tests\Unit\ExampleTest</span></div><div class="l">&nbsp;&nbsp;<span class="green">✓</span> parent</div><div class="l">&nbsp;&nbsp;<span class="green">✓</span> child</div><div class="l">&nbsp;</div><div class="l"><span class="gray">Tests:</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="green b">2 passed</span> <span class="gray">(3 assertions)</span></div><div class="l"><span class="gray">Duration:</span>&nbsp;&nbsp;&nbsp;<span class="white">0.05s</span></div></div>

If the `parent` test fails, the `child` test will be bypassed, and an informative message will be displayed in your test results:

```php
test('parent', function () {
    expect(true)->toBeFalse();
});

test('child', function () {
    expect(false)->toBeFalse();
})->depends('parent');
```

The example above will result in the following output:

<div class="terminal not-prose"><div class="l">&nbsp;&nbsp;<span class="chip fail">FAIL</span>&nbsp;&nbsp;<span class="white">Tests\Unit\ExampleTest</span></div><div class="l">&nbsp;&nbsp;<span class="red">⨯</span> parent</div><div class="l">&nbsp;&nbsp;<span class="amber">-</span> child <span class="gray">→</span> <span class="amber">This test depends on "parent" to pass</span></div></div>

It is important to remember that the `it()` function prefixes the test with "it" by default. Thus, when referencing the test name via the `depends()` method, you should include the "it " prefix:

```php
it('is the parent', function () {
    expect(true)->toBeTrue();
});

test('child', function () {
    expect(false)->toBeFalse();
})->depends('it is the parent');
```

This results in the following output:

<div class="terminal not-prose"><div class="l">&nbsp;&nbsp;<span class="chip pass">PASS</span>&nbsp;&nbsp;<span class="white">Tests\Unit\ExampleTest</span></div><div class="l">&nbsp;&nbsp;<span class="green">✓</span> it is the parent</div><div class="l">&nbsp;&nbsp;<span class="green">✓</span> child</div><div class="l">&nbsp;</div><div class="l"><span class="gray">Tests:</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="green b">2 passed</span> <span class="gray">(2 assertions)</span></div><div class="l"><span class="gray">Duration:</span>&nbsp;&nbsp;&nbsp;<span class="white">0.03s</span></div></div>

Parent tests may even provide return values that can be accessed as arguments in the `child` test:

```php
test('parent', function () {
    expect(true)->toBeTrue();

    return 'from parent';
});

test('child', function ($parentValue) {
    var_dump($parentValue); // from parent

    expect($parentValue)->toBe('from parent');
})->depends('parent');
```

You may also add multiple dependencies to a test. However, all parent tests must pass, and the values returned by each test will be available as function parameters in the same order as the specified dependencies:

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

While test dependencies are uncommon, they can be helpful for optimizing your tests and minimizing the need to recreate resources repeatedly. In the next chapter, we will explore how you may create plugins: [Creating Plugins](/docs/creating-plugins)
