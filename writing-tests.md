---
title: Writing Tests
description: Writing Tests
---

# Writing Tests

- [Overview](#overview)
- [Assertations or expectations](#assertations-or-expectations)

<a name="overview"></a>

## Overview

In our Files & Folders section, we show you how to create and organize your
test files, typically suffixed with `Test.php`, such as `ExampleTest.php`.

Let's take a look at a simple example. Imagine your project offers a global function called sum that adds two numbers together. To test this function, you would create a `SumTest.php` file with the following code:

```php
<?php

test('sum', function () {
    $result = sum(1, 2);

    expect($result)->toBe(3);
    // or
    $this->assertSame(3, $result);
});
```

When you run ``./vendor/bin/pest`, Pest will print the message "✓ sum", indicating that the test has passed:

```shell
   PASS  Tests\SumTest.php
  ✓ sum

  Tests:  1 passed
```

In addition to the test function, we also offer the convenient `it` function, which prefixes the description with the word "it". For example:

```php
<?php

it('performs sums', function () {
    $result = sum(1, 2);

    expect($result)->toBe(3);
    // or...
    $this->assertSame(3, $result);
});
```

This time, the result would be the following: "it performs sums".

```shell
   PASS  Tests\SumTest.php
  ✓ it performs sums

  Tests:  1 passed
```

<a name="assertations-or-expectations"></a>
## Assertations or expectations

In the example above, you saw the following code snippet:

```php
expect($result)->toBe(3); // Expectation API
// or...
$this->assertSame(3, $result); // Assertion API
```

Both APIs have the same goal: to perform checks to ensure that
things are going as planned. Historically, because Pest is
built on top of PHPUnit, we have always provided access
to the Assertion API. However, we recommend you take a look
at the Expectation API, as it allows you to write your tests
like a natural sentence, making it easier to read and understand.


---

Continue to our next section, for more information on how to use the Expectation API: [Expectations →](/docs/expectations)