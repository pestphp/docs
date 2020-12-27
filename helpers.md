---
title: Custom Helpers
description: Custom Helpers
---

# Custom helpers

- [Overview](#overview)
- [Usage](#usage)

<a name="overview"></a>
## Overview

While Pest is very powerful out-of-the-box, you may have some testing code specific to your
project that you don't want to repeat in every test.

Creating custom helpers can improve readability and reduce the amount of code of your test suite.

<a name="usage"></a>
## Usage

By default, Pest autoloads the `tests/Pest.php` file. In this file,
you can place your custom helpers. Of course, if those helpers are specific
to a test file, you can place them inside of that test file only.

For example, imagine that you want to make a custom helper for the `actingAs` Laravel helper:
```php
<?php // tests/Pest.php

/**
 * Set the currently logged in user for the application.
 *
 * @return TestCase
 */
function actingAs(Authenticatable $user, string $driver = null)
{
    return test()->actingAs($user, $driver);
}
```

> **TIP**: use the `test` function without any arguments to get
access to the current test case.

Now, you can use the `actingAs` helper in your tests:
```php
<?php

it('redirects to user profile', function () {
    $user = factory(User::class)->create();

    actingAs($user)->get('/profile')->assertSee($user->name);
});
```

> **TIP**: You can use custom helpers in higher order tests as if the helper was in the current test case.

Next section: [Exceptions & Errors →](/docs/exceptions-and-errors)

