---
title: Laravel Plugin
description: The Laravel Plugin
---

# Laravel Plugin

- [Overview](#overview)
- [Installation](#installation)
- [Available Artisan commands](#available-artisan-commands)
- [Available Functions](#available-functions)
  - [assertDatabaseCount](#assert-database-count) 
- [Available Expectations](#available-expectations)
- [Using Test Traits](#using-test-traits)
- [Tests without description](#tests-without-description)
- [Laravel Dusk](#laravel-dusk)

<a name="overview"></a>
## Overview

Although Pest is framework agnostic, it was built with Laravel in mind. The Laravel Plugin for Pest gives your direct access to Laravel's testing API in your test files.

**Source code**: [github.com/pestphp/pest-plugin-laravel](https://github.com/pestphp/pest-plugin-laravel)

<a name="installation"></a>
## Installation

Install the Laravel Plugin via the Composer package manager:

```bash
composer require pestphp/pest-plugin-laravel --dev
```

Since Pest is a **progressive testing framework**, your current test suite will still work, even if you run it with the `./vendor/bin/pest` console command.

<a name="available-artisan-commands"></a>
## Available Artisan Commands

<div class="collection-method-list" markdown="1">

- [`pest:install`](#artisan-pest-install)
- [`pest:test`](#artisan-pest-test)
- [`pest:dataset`](#artisan-pest-dataset)

</div>

<a name="artisan-pest-install"></a>
### `pest:install`

The `pest:install` Artisan command creates the `tests/Pest.php` file in your tests folder:

```
php artisan pest:install
```

As described on the [Underlying Test Case](/docs/underlying-test-case) section,
the `Pest.php` file is the place where all your `uses` should live.

<a name="artisan-pest-test"></a>
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

<a name="artisan-pest-dataset"></a>
### `pest:dataset`

The `pest:dataset` Artisan command creates a new dataset in the `tests/Datasets` folder:

```
php artisan pest:dataset Emails
```

As detailed on the [Datasets](/docs/datasets) section, datasets allow you to
run the same test multiple times with different data.

<a name="available-functions"></a>
## Available functions

Laravel provides several assertions for your feature tests. You may access those assertions
as usual with the `$this` variable, or may import and use the functions under the `Pest\Laravel` namespace
to have easy access Laravel's testing API. As example, the `get` function
makes a `GET` request into the application:

```php
use function Pest\Laravel\get;

it('has a welcome page', function () {
    get('/')->assertStatus(200);

    // Same as:
    $this->get('/')->assertStatus(200);
});
```

<a name="assert-database-count"></a>
### `assertDatabaseCount()`

Asserts count of database table.

```php
assertDatabaseCount('users', 5);
```

<a name="available-expectations"></a>
## Available Expectations

Laravel Plugin offers you a set of additional expectations.

<a name="expect-toBeCollection"></a>
### `toBeCollection()`

Asserts that the value is an instance of `\Illuminate\Support\Collection`

```php
expect(collect([1, 2, 3]))->toBeCollection();
```

<a name="using-test-traits"></a>
## Using Test Traits

Laravel includes multiple test traits that allow you to configure the setUp of your test suite.

As explained in the [Underlying Test Case](/docs/underlying-test-case) section, the usage
of traits in Pest is possible with the `uses` function.

As example, here is how you can use the `RefreshDatabase` trait in Pest:

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

<a name="tests-without-description"></a>
## Tests without description

Also with the Laravel plugin, tests can be written without description. This
lets you write even simpler tests than before:

```php
use function Pest\Laravel\get;

get('/')->assertStatus(200);
```

Note that Pest will automatically generate a description for these tests:

```bash
✓ get '/' → assertStatus 200
```

To import multiple functions, wrap them in curly brackets like so:

```php
use function Pest\Laravel\{get, getJson};

get('/')->assertStatus(200);

getJson('api/posts')->assertStatus(200);
```

Once again, Pest will generate a description for each test:

```bash
✓ get '/' → assertStatus 200
✓ getJson 'api/posts' → assertStatus 200
```

Remember, for the full list of available Laravel testing methods, please refer to the [Laravel documentation](https://laravel.com/docs/master/http-tests).

<a name="laravel-dusk"></a>
## Laravel Dusk

Pest can also work seamlessly with [Laravel Dusk](https://laravel.com/docs/dusk). To get started, first follow Dusk's [installation instructions](https://laravel.com/docs/7.x/dusk#installation).

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

Now, you can run your Dusk tests via `php artisan pest:dusk`.

> **Note:** As for now, the Laravel plugin doesn't support Dusk's functions, so you'll have to use them with the `$this` variable as the example above shows.

---

Next section: [Livewire →](/docs/plugins/livewire)
