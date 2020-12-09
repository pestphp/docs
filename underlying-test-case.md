---
title: Underlying Test Case
description: Underlying Test Case
---

# Underlying Test Case

- [Overview](#overview)

<a name="overview"></a>
## Overview
The closure you provide to your test function is always bound to a specific
test case class. By default, that class is `PHPUnit\Framework\TestCase`:

```php
<?php

it('has home', function () {
    $this->assertTrue(true);

    // \PHPUnit\Framework\TestCase
    echo get_class($this); 
});
```

In real-world applications, you may have to change it, and for that, you
can call the `uses` function:

### `uses()`

The `uses` function binds a class and/or trait to your test files.
This is how you would bind a trait to your current file:

```php
<?php

use Illuminate\Foundation\Testing\RefreshDatabase;

// Uses the given trait in the current file
uses(RefreshDatabase::class);
```

The `in()` function lets you use the given class and/or trait recursively inside the specified folder:

```php
<?php

use Tests\TestCase;

// Uses the given test case in the "Feature" folder recursively
uses(TestCase::class)->in('Feature');
```

You can use multiple `uses()` calls if they target **different folders**:

```php
<?php

use Tests\TestCase;
use Illuminate\Foundation\Testing\RefreshDatabase;

// Uses the given test case in the "Feature" folder recursively
uses(TestCase::class)->in('Feature');

// Uses the given trait in the "Unit" folder recursively
uses(RefreshDatabase::class)->in('Unit');
```


To bind multiple classes and/or traits at once, group them in your `uses()` call like so:

```php
<?php

use Tests\TestCase;
use Illuminate\Foundation\Testing\RefreshDatabase;

// Uses the given test case and trait in the current folder recursively
uses(TestCase::class, RefreshDatabase::class)->in(__DIR__);
```

In some cases it is not enough to just add the trait with `uses()` you can also call a function on the trait. 
Can be done like this: 
```php
uses(CreatesApplication::class);
beforeEach()
    ->createApplication();
```

## `tests/Pest.php`

Please consider creating a file with the name `tests/Pest.php` on the
root directory of your tests folder. No worries, that file
is autoloaded automatically.
It's an ideal place to recursively bind traits and classes to your tests with the `uses()` function.

```
tests
    - Unit/ComponentTest.php
    - Feature/HomeTest.php
    - Pest.php <--
phpunit.xml
```

Next section: [Assertions →](/docs/assertions)

