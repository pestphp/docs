---
title: Test Dependencies
description: Sometimes, tests require certain preconditions or events to occur prior to their execution or else they will not succeed. For example, you may only be able to verify that users are able to modify their accounts if you have first verified that an account can be established.
---

# Test Dependency

Sometimes, tests require certain preconditions or events to occur prior to their execution or else they will not succeed. For example, you may only be able to verify that users are able to modify their accounts if you have first verified that an account can be established.

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

In this example, the `child` test will be triggered once the `parent` test has successfully completed.

<div class="code-snippet">
    <img src="/assets/img/depends.webp?1" style="--lines: 6" />
</div>

If the `parent` test fails, the `child` test will be bypassed and an informative message will be displayed in your test results.

```php
test('parent', function () {
    expect(true)->toBeFalse();
});

test('child', function () {
    expect(false)->toBeFalse();
})->depends('parent');
```

The example above results in the following output:

<div class="code-snippet">
    <img src="/assets/img/depends-fail.webp?1" style="--lines: 3" />
</div>

It is important to remember that the `it()` function prefixes the test with "it" by default. Thus, when referencing the test name via the `depends()` method, you should include the "it " prefix.

```php
it('is the parent', function () {
    expect(true)->toBeTrue();
});

test('child', function () {
    expect(false)->toBeFalse();
})->depends('it is the parent');
```

Results is:

<div class="code-snippet">
    <img src="/assets/img/depends-pass.webp?1" style="--lines: 6" />
</div>

Parent tests can even provide return values that can be accessed as arguments in the `child` test.

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

It is also possible to add multiple dependencies to a test. However, all parent tests must pass, and the values returned by each test will be available as function parameters in the same order as the specified dependencies.

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

While test dependencies are uncommon, they can be useful for optimizing your tests and minimizing the need to recreate resources repeatedly. In the next chapter, we will explore how you can create plugins: [Creating Plugins](/docs/creating-plugins)
