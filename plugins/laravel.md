---
title: Laravel Plugin
description: If you appreciate having a few extra artisan commands and enjoy using namespaced functions, then this plugin is perfect for you. It will enhance your experience with Laravel and Pest.
---

# Laravel Plugin

**Source code**: [github.com/pestphp/pest-plugin-laravel](https://github.com/pestphp/pest-plugin-laravel)

Many Pest users have a misconception that the Laravel plugin is essential for Pest to function properly with Laravel, but this is not true. The plugin only provides access to two things for convenience: artisan commands and namespaced functions.

If you appreciate having a few extra artisan commands and enjoy using namespaced functions, then this plugin is perfect for you. It will enhance your experience with Laravel and Pest.

To get started, you need to require this plugin via Composer.

```bash
composer require pestphp/pest-plugin-laravel --dev
```

After installation, you can use the extra Artisan commands that the Pest plugin Laravel provides, as well as the namespaced functions for your convenience.

## Artisan Commands

To generate a new test in the `tests/Feature` directory, utilize the `pest:test` Artisan command.

```bash
php artisan pest:test UsersTest
```

You have the option to specify the `--unit` flag when creating a test to have it placed in the `tests/Unit` directory.

```bash
php artisan pest:test UsersTest --unit
```

By executing the `pest:dataset` Artisan command, you can produce a fresh dataset in the `tests/Datasets` directory.

```
php artisan pest:dataset Emails
```

As you may know, Laravel Laravel provides several assertions for your feature tests. You may access those assertions as usual with the `$this` variable.

```php
it('has a welcome page', function () {
    $this->get('/')->assertStatus(200);
});
```

With the assistance of this plugin, it is possible for you to bypass the `$this` variable and use the namespaced functions that are provided by this plugin.

```php
use function Pest\Laravel\{get};

it('has a welcome page', function () {
    get('/')->assertStatus(200);
    // same as $this->get('/')...
});
```

As expected, all of the assertions that were previously accessed through `$this->` are now available as namespace functions.

```php
use function Pest\Laravel\{actingAs, get, post, delete, ...};
```

---



In this section, we have covered Pest's Laravel plugin. In the next section, we are going to cover the Livewire plugin: []

Next section: [Livewire →](/docs/plugins/livewire)
