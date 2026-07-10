---
title: Plugins
description: Discover the official and community plugins we endorse, adding namespaced functions, console commands, custom expectations, and command-line options to Pest.
---

# Plugins

In this section, we will discuss the official and community-developed plugins that we endorse. Plugins primarily offer namespaced functions, console commands, custom expectations, and additional command-line options that augment the default Pest experience.

If you are a plugin developer, please consult our [documentation on creating plugins](/docs/creating-plugins) for more information on building your own Pest plugins.

The following plugins are maintained by the Pest team:

- [Faker](#faker)
- [Laravel](#laravel)
- [Livewire](#livewire)

---

<a name="faker"></a>
## Faker

**Source code**: [github.com/pestphp/pest-plugin-faker](https://github.com/pestphp/pest-plugin-faker)

To get started with Pest's Faker plugin, require the plugin via Composer:

```bash
composer require pestphp/pest-plugin-faker --dev
```

Once the plugin is installed, you may use the namespaced `fake` function to generate fake data for your tests:

```php
use function Pest\Faker\fake;

it('generates a name', function () {
    $name = fake()->name; // random name...

    //
});
```

You may also designate the "locale" that the `fake()` function should use by passing it to the function:

```php
use function Pest\Faker\fake;

it('generates a portuguese name', function () {
    $name = fake('pt_PT')->name; // Nuno Maduro

    //
});
```

To learn more about Faker, including comprehensive details about the API it provides, please consult [its official documentation](https://fakerphp.github.io/).

---

<a name="laravel"></a>
## Laravel

**Source code**: [github.com/pestphp/pest-plugin-laravel](https://github.com/pestphp/pest-plugin-laravel)

To get started with Pest's Laravel plugin, require the plugin via Composer:

```bash
composer require pestphp/pest-plugin-laravel --dev
```

This plugin adds additional Artisan commands and functions to the default Pest installation. For example, to generate a new test in the `tests/Feature` directory, you may now use the `pest:test` Artisan command:

```bash
php artisan pest:test UsersTest
```

You may provide the `--unit` option when creating a test to place the test in the `tests/Unit` directory:

```bash
php artisan pest:test UsersTest --unit
```

Executing the `pest:dataset` Artisan command will create a fresh dataset in the `tests/Datasets` directory:

```bash
php artisan pest:dataset Emails
```

As you may know, Laravel provides a variety of assertions you may take advantage of in your feature tests. When using Pest's Laravel plugin, you may access all of those assertions as you typically would:

```php
it('has a welcome page', function () {
    $this->get('/')->assertStatus(200);
});
```

In addition, this plugin allows you to bypass the `$this` variable while using namespaced functions such as `actingAs`, `get`, `post`, and `delete`:

```php
use function Pest\Laravel\{get};

it('has a welcome page', function () {
    get('/')->assertStatus(200);
    // same as $this->get('/')...
});
```

To illustrate this convenient feature with another example, let's write a test acting as an authenticated user accessing the restricted dashboard page:

```php
use App\Models\User;
use function Pest\Laravel\{actingAs};

test('authenticated user can access the dashboard', function () {
    $user = User::factory()->create();

    actingAs($user)->get('/dashboard')
        ->assertStatus(200);
});
```

As you would expect, all of the assertions that were previously accessible via `$this->` are available as namespaced functions:

```php
use function Pest\Laravel\{actingAs, get, post, delete, ...};
```

You may find the full testing documentation on the Laravel website: [laravel.com/docs/12.x/testing](https://laravel.com/docs/12.x/testing).

---

<a name="livewire"></a>
## Livewire

**Source code**: [github.com/pestphp/pest-plugin-livewire](https://github.com/pestphp/pest-plugin-livewire)

To get started with Pest's Livewire plugin, require the plugin via Composer:

```bash
composer require pestphp/pest-plugin-livewire --dev
```

Once the plugin is installed, you may use the `livewire` namespaced function to access your Livewire components:

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

In this section, we have seen how plugins can enhance your Pest experience. Next, let's see how you may manage your team's tasks and responsibilities using Pest: [Team Management](/docs/team-management)
