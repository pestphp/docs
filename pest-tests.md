---
title: Pest Tests
description: Pest test files
---

# Pest Tests

- [Test files](#test-files)
- [Pest syntax](#pest-syntax)
- [PHPUnit syntax](#phpunit-syntax)

<a name="test-files"></a>

## Test files

Pest test files are clean and simple and easy to read. There is no need for classes and no boilerplate code.

Pest offers you two functions for writing a Test: `test()` and `it()`.

You can use them as you prefer, both functions share the same behavior.

<a name="pest-syntax"></a>

## Pest syntax

To write a test, need to pass a `test description` followed by a `closure` containing your test logic.

> 💡 A closure is an anonymous function passed as an argument to another function.

The next example demonstrates the `test()` function:

```php
<?php
// tests/Unit/MyTest.php

test('TRUE is TRUE', function () {
    $this->assertTrue(true);   // assertion

    expect(true)->toBeTrue();  // expectation
});
```

And now, and the same example using the `it()` function.

When using `it()`, Pest appends "it" to your test description behind the scenes.


```php
<?php
// tests/Unit/MyTest.php

it('asserts that TRUE is TRUE', function () {
    $this->assertTrue(true);   // assertion

    expect(true)->toBeTrue();  // expectation
});
```

Running `MyTest.php` results in:

```shell
   PASS  Tests\Unit\MyTest
  ✓ TRUE is TRUE
  ✓ it asserts that TRUE is TRUE

  Tests:  2 passed
```

<br/>

## PHPUnit syntax

Just for comparison, here is the PHPUnit version of the example featured in this section.

Tests written with PHPUnit syntax are fully compatible with Pest, and will run as expected.

```php
<?php
// tests/Unit/MyTest.php

namespace Tests\Feature;

use Tests\TestCase;

class ExampleTest extends TestCase
{
    /**
     * A basic test example.
     *
     * @return void
     */
    public function test_true_is_true()
    {
        $this->assertTrue(true);
    }
}
```

---

**Next**: Let's start [`Write a Test` →](/docs/write-a-test)