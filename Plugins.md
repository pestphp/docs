---
title: Plugins
description: In this section, we will discuss the official and community plugins that we endorse. It's a common misconception among Pest users that plugins are necessary for Pest to integrate smoothly with their frameworks. However, this is not the case. Plugins primarily offer namespaced functions, console commands, and additional options.
---

# Plugins

In this section, we will discuss the official and community plugins that we endorse. It's a common misconception among Pest users that plugins are necessary for Pest to integrate smoothly with their frameworks. However, this is not the case. Plugins primarily offer namespaced functions, console commands, and additional command-line options.

If you are a plugin developer, please consult the [Creating Plugins](/docs/creating-plugins) section for more information on how to create Pest plugins.

<div class="collection-method-list" markdown="1">

- [faker](#faker)
- [money](#money)
- [Laravel](#laravel)
- [Livewire](#livewire)

</div>

---

<a name="faker"></a>
## Faker

**Source code**: [github.com/pestphp/pest-plugin-faker](https://github.com/pestphp/pest-plugin-faker)

To get started, you need to require this plugin via Composer.

```bash
composer require pestphp/pest-plugin-faker --dev
```

After finishing the installation, you can opt to utilize the fake namespaced function to generate fake data for your tests.

```php
use function Pest\Faker\fake;

it('generates a name', function () {
    $name = fake()->name; // random name...

    //
});
```

You can also designate the "locale" to be utilized by the `fake()` function.

```php
use function Pest\Faker\faker;

it('generates a portuguese name', function () {
    $name = faker('pt_PT')->name; // Nuno Maduro

    //
});
```

To obtain additional information on Faker, including comprehensive details about the API it provides, please consult the [documentation for Faker PHP](https://fakerphp.github.io/).

<a name="laravel"></a>
## Laravel

**Source code**: [github.com/pestphp/pest-plugin-laravel](https://github.com/pestphp/pest-plugin-laravel)

To get started, you need to require this plugin via Composer.

```bash
composer require pestphp/pest-plugin-laravel --dev
```

After installation, you can use the extra Artisan commands that the Pest plugin Laravel provides, as well as the namespaced functions for your convenience.

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

<a name="livewire"></a>
## Livewire

**Source code**: [github.com/pestphp/pest-plugin-livewire](https://github.com/pestphp/pest-plugin-livewire)

To get started, you need to require this plugin via Composer.

```bash
composer require pestphp/pest-plugin-livewire --dev
```

Upon completion of the installation, you have the option to use the `livewire` namespaced function, as an alternative to `$this->livewire`, for accessing your Livewire components.

```php
use function Pest\Livewire\livewire;

it('can be incremented', function () {
    livewire(Counter::class)
        ->call('increment')
        ->assertSee(1);
});

it('can be decremented', function () {
    livewire(Counter::class)
        ->call('decrement')
        ->assertSee(-1);
});
```

---



In this section, we have seen how plugins can enhance your Pest experience. Now, let's dive into architectural testing and how it can benefit your development process. By performing architectural testing, you can evaluate the overall design of your application and identify potential flaws before they become significant issues: [Arch Testing](/docs/arch-testing)