---
title: Laravel Guide
description: The Laravel Guide
---

# Laravel Guide

- [Overview](#overview)
    - [Installation](#installation)
- [Artisan commands](#artisan-commands)
    - [pest:install](#pest-install-command)
    - [pest:test](#pest-test-command)
    - [pest:dataset](#pest-dataset-command)
- [Traits](#traits)
- [Laravel Dusk](#laravel-dusk)

<a name="overview"></a>
## Overview

Although Pest is framework agnostic, it was built with Laravel in mind. In this guide,
we will see how you can create tests with the elegance you expect, and
the convenience you love.

<a name="installation"></a>
### Installation

First, [install Pest](/docs/installation) in your Laravel application. Since Pest is a **progressive testing framework**,
your current test suite will still work, even if you run it with the `./vendor/bin/pest` console command.

Now, let's get started by taking a look at Laravel's default test suite:

```bash
- tests
    - Feature
        - ExampleTest.php
    - Unit
        - ExampleTest.php
    - CreatesApplication.php
    - TestCase.php
- phpunit.xml
```

First, let's run the `pest:install` artisan command:
```bash
php artisan pest:install
```

This command will create the `Pest.php` file with the following code:
```php
<?php

uses(Tests\TestCase::class)->in('Feature');
```

Next, let's migrate the `tests/Unit/ExampleTest.php` file to Pest:
```php
<?php

test('basic')->assertTrue(true);
```

And now, the `tests/Feature/ExampleTest.php` file:
```php
<?php

it('has welcome page')
    ->get('/')
    ->assertStatus(200);
```

That's it! You should now have a fresh Laravel installation with
a Pest test suite.

---

<a name="artisan-commands"></a>
## Artisan commands

<a name="pest-install-command"></a>
### `pest:install`

The `pest:install` Artisan command creates the `test/Pest.php` file in your tests folder:

```
php artisan pest:install
```

As described on the [Underlying Test Case](/docs/underlying-test-case) section,
the `Pest.php` file is the place where all your `uses` should live.

<a name="pest-test-command"></a>
### `pest:test`

The `pest:test <name>` Artisan command creates a new test in the `tests/Feature` folder:

```
php artisan pest:test UsersTest
```

Optionally, you can provide the `--unit` option to create the
given test under the `tests/Unit` folder:

```
php artisan pest:test UsersTest --unit
```

<a name="pest-dataset-command"></a>
### `pest:dataset`

The `pest:dataset` Artisan command creates a new dataset in the `tests/Datasets` folder:

```
php artisan pest:dataset Emails
```

As detailed on the [Datasets](/docs/datasets) section, datasets allow you to
run the same test multiple times with different data.

---

<a name="traits"></a>
## Traits

As explained in the [Underlying Test Case](/docs/underlying-test-case) section, the usage
of traits in Pest is possible with the `uses` function:

```php
<?php

use App\User;
use Illuminate\Foundation\Testing\RefreshDatabase;

uses(RefreshDatabase::class);

beforeEach(fn () => User::factory()->create());

it('has users')->assertDatabaseHas('users', [
    'id' => 1,
]);
```

Keep in mind that you can avoid repeating the `uses(RefreshDatabase::class)`
line in each of your tests by [binding it](/docs/underlying-test-case) in your `Pest.php` file.

---

<a name="laravel-dusk"></a>
## Laravel Dusk

Pest can also work seamlessly with [Laravel Dusk](https://laravel.com/docs/dusk).

First, follow Dusk's [installation instructions](https://laravel.com/docs/7.x/dusk#installation).

Then, add the "Browser" test suite under the `<testsuites>` tag in your `phpunit.xml` file:

```xml
<testsuites>
    ...
    <testsuite name="Browser">
        <directory suffix="Test.php">./tests/Browser</directory>
    </testsuite>
</testsuites>
```

Finally, instruct Pest to use `DuskTestCase` as the base test case for the `Browser` directory by adding the following code to your `Pest.php` file:

```php
use Tests\DuskTestCase;

uses(DuskTestCase::class)->in('Browser');
```

That's it! Now you can write Dusk tests using Pest's syntax:

```php
<?php

use Laravel\Dusk\Browser;

it('has homepage', function () {
    $this->browse(function (Browser $browser) {
        $browser->visit('/')
            ->assertSee('Pest');
    });
});
```

> **Note:** You can still use Dusk's regular class-based syntax as you can do with any other tests.

Now, when you run `pest` you will see your Dusk tests as well.

> **Note:** As for now, the Laravel plugin doesn't support Dusk's functions, so you'll have to use them with the `$this` variable as the example above shows.

---

Next section: [Symfony →](/docs/guides/symfony)
