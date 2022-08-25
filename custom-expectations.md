---
title: Custom Expectations
description: Custom Expectations
---

# Custom Expectations

- [Overview](#overview)
- [Usage Example](#usage-example)
- [Displaying custom messages](#displaying-custom-messages)

<a name="overview"></a>

## Overview

When writing tests, you might find yourself in need of a custom rule for a check or repeating the same set of checks in different tests.

Pest [Expectation API](/docs/expectations) can be easily extended to create your Custom Expectations.

Custom Expectations are defined using the `expect()->extend()` function inside the `tests/Pest.php` file.

The Expectation `$value` can be accessed with `$this->value`.

<a name="usage-example"></a>

## Usage Example

Let's say that are frequently asserting that numbers appear within a particular range of other numbers.

You could abstract this code into a `toBeWithinRange()` Custom Expectation, promoting code reuse and improving readability.

```php
// tests/Unit/MyTest.php

test('numeric ranges', function () {
    expect(100)->toBeWithinRange(90, 110);
});
```

Defined here:

```php
// tests/Pest.php
<?php 

expect()->extend('toBeWithinRange', function (int $min, int $max) {
    return $this->toBeGreaterThanOrEqual($min)
                ->toBeLessThanOrEqual($max);
});
```

<a name="displaying-custom-messages"></a>

## Displaying custom messages

For the next example, let's say you need to check if a `number is within the range 1000-2000 and is even`. This is the "special range".

We can reuse the previous Custom Exception. Then, add  a check which throws a custom message if `$this->value` is not an even number.

```php
// tests/Pest.php

<?php 

use PHPUnit\Framework\ExpectationFailedException;

expect()->extend('toBeSpecialRange', function () {
    expect($this->value)->toBeWithinRange(1000, 2000);
    
    if ($this->value % 2 !== 0) {
        throw new ExpectationFailedException("{$this->value} is not an even number.");
    }

    return $this;
});
```

In our test file, we can now use:

```php
// tests/Unit/MyTest.php

test('special range', function () {
    expect(1005)->toBeSpecialRange();
});
```

And this test fails, as expected:

```shell 
   FAIL  Tests\Unit\MyTest
  ⨯ special range

  ---

  • Tests\Unit\MyTest > special range
  1005 is not an even number.
```

---

Next section: [Setup And Teardown →](/docs/setup-and-teardown)
