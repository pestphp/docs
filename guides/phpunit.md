---
title: PHPUnit Guide
description: PHPUnit Guide
---

# PHPUnit Guide

- [Overview](#overview)
    
<a name="overview"></a>
## Overview

Pest is built on top of PHPUnit, and since Pest is a **progressive testing framework**, you can
have both PHPUnit classes and Pest test files in the same test suite.

In other words, if you have followed the [PHPUnit](https://phpunit.de/) *Getting Started* guide, you really just
have to install Pest on top of it - as referenced in the [installation guide](/docs/installation).

Now, in this guide, we are going to transform a PHPUnit test into a Pest test.

> **Disclaimer**: PHPUnit is great! Pest just provides a different API to create tests.

Let's take this PHPUnit test from the [Laravel.io website](https://laravel.io):
```php
<?php

namespace Tests\Feature;

use Illuminate\Foundation\Testing\DatabaseMigrations;
use Tests\TestCase;

class HomeTest extends TestCase
{
    use DatabaseMigrations;

    /** @test */
    public function users_can_see_the_homepage()
    {
        $this->visit('/')
            ->see('Laravel.io')
            ->see('The Laravel Community Portal');
    }
}
```

Looking at this code, only 2 things are important:

- There is a **home page**
- When users visit that home page they **see** "Laravel.io" and  "The Laravel Community Portal".

So let's put only what really matters into our Pest test:
```php
<?php

use Tests\Feature;
use Illuminate\Foundation\Testing\DatabaseMigrations;

uses(TestCase::class, DatabaseMigrations::class);

it('has home page', function () {
    $this->visit('/')
        ->see('Laravel.io')
        ->see('The Laravel Community Portal');
});
```

Next, remember [higher order tests](/docs/higher-order-tests)? They are shortcuts for
performing common actions while writing your tests. So let's apply this principle in our test:

```php
<?php

use Tests\Feature;
use Illuminate\Foundation\Testing\DatabaseMigrations;

uses(TestCase::class, DatabaseMigrations::class);

it('has home page')
    ->get('/')
    ->see('Laravel.io')
    ->see('The Laravel Community Portal');
```

Finally, the [Underlying Test Case](/docs/underlying-test-case/) of this test
is `TestCase` and our test also uses Laravel's `DatabaseMigrations`. So
let's move those in our `tests/Pest.php` file:
```php
<?php

use Tests\Feature;
use Illuminate\Foundation\Testing\DatabaseMigrations;

uses(TestCase::class, DatabaseMigrations::class)->in('Feature');
```

Let's cleanup our test, now that we no longer need those local `uses`:
```php
<?php

it('has home page')
    ->get('/')
    ->see('Laravel.io')
    ->see('The Laravel Community Portal');
```

This Pest test is equivalent to the PHPUnit one, and it only has 6 lines of code, down from the
19 original lines. And most importantly, the test now contains only what really matters.

Wouldn't it be cool if you could automatically migrate your PHPUnit tests to Pest? We got you! In the next section, you will learn all about Drift, our experimental migration tool. 🔥

Next section: [Drift →](/docs/guides/drift)
