---
title: Running a single test
description: Running a single test
---

# Running a single test

## Overview

If you would like to run a single test to debug a problem, just use the `only()` method:

```php
<?php
// tests/Unit/MyTest.php

test('this will not run', function () {
    expect(true)->toBeTrue();
});

it('has a homepage', function () {
    expect(true)->toBeTrue();
})->only();
```

Results in:

```shell
   PASS  Tests\Unit\MyTest
  ✓ it has a homepage

  Tests:  1 passed
``` 

> Please be aware that `->only()` requires all tests to be written with Pest test functions to work correctly. Furthermore, it will be ignored if the `--ci` option is added to the cli command

Next section: [Datasets →](/docs/datasets)
