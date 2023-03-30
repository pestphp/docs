---
title: Plugins
description: In this section, we will discuss the official and community developed plugins that we endorse. Plugins primarily offer namespaced functions, console commands, custom expectations, and additional command-line options to augment the default Pest experience.
---

# Plugins

In this section, we will discuss the official and community developed plugins that we endorse. Plugins primarily offer namespaced functions, console commands, custom expectations, and additional command-line options to augment the default Pest experience.

If you are a plugin developer, please consult our [documentation on creating plugins](/docs/creating-plugins) for more information on how to create Pest plugins.

The following plugins are maintained by the Pest team:

- [Faker](#faker)
- [Laravel](#laravel)
- [Livewire](#livewire)
- [Watch](#watch)

---

<a name="faker"></a>
## Faker

**Source code**: [github.com/pestphp/pest-plugin-faker](https://github.com/pestphp/pest-plugin-faker)

To start using Pest's Faker plugin, you need to require the plugin via Composer.

```bash
composer require pestphp/pest-plugin-faker --dev
```

After installing the plugin, you may utilize the namespaced `fake` function to generate fake data for your tests.

```php
use function Pest\Faker\fake;

it('generates a name', function () {
    $name = fake()->name; // random name...

    //
});
```

You can also designate the "locale" that should be utilized by the `fake()` function by providing the locale to the function.

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

To start using Pest's Laravel plugin, you need to require this plugin via Composer.

```bash
composer require pestphp/pest-plugin-laravel --dev
```

This plugin adds additional Artisan commands and functions to the default Pest installation. For example, to generate a new test in the `tests/Feature` directory, you can now utilize the `pest:test` Artisan command.

```bash
php artisan pest:test UsersTest
```

You may provide the `--unit` option when creating a test to place the test in the `tests/Unit` directory.

```bash
php artisan pest:test UsersTest --unit
```

Executing the `pest:dataset` Artisan command will create a fresh dataset in the `tests/Datasets` directory.

```bash
php artisan pest:dataset Emails
```

As you may know, Laravel provides a variety of assertions you can take advantage of in your feature tests. When using Pest's Laravel plugin, you may access all of those assertions as you typically would.

```php
it('has a welcome page', function () {
    $this->get('/')->assertStatus(200);
});
```

In addition, with the assistance of this plugin, it is possible for you to bypass the `$this` variable while using namespaced functions such as `actingAs`, `get`, `post` and `delete`.

```php
use function Pest\Laravel\{get};

it('has a welcome page', function () {
    get('/')->assertStatus(200);
    // same as $this->get('/')...
});
```

To illustrate this convenient feature using another example, we can write a test acting as an authenticated user accessing the restricted dashboard page.

```php
use App\Models\User;
use function Pest\Laravel\{actingAs};

test('authenticated user can access the dashboard', function () {
    $user = User::factory()->create();

    actingAs($user)->get('/dashboard')
        ->assertStatus(200);
});
```

As you may expect, all of the assertions that were previously accessible via `$this->` are available as namespace functions.

```php
use function Pest\Laravel\{actingAs, get, post, delete, ...};
```

You can find the full testing documentation on the Laravel website: [laravel.com/docs/10.x/testing](https://laravel.com/docs/10.x/testing).

---

<a name="livewire"></a>
## Livewire

**Source code**: [github.com/pestphp/pest-plugin-livewire](https://github.com/pestphp/pest-plugin-livewire)

To install Pest's Livewire plugin, you need to require the plugin via Composer.

```bash
composer require pestphp/pest-plugin-livewire --dev
```

After installing the plugin, you may use the `livewire` namespaced function to access your Livewire components.

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

<a name="watch"></a>
## Watch

**Source code**: [github.com/pestphp/pest-plugin-watch](https://github.com/pestphp/pest-plugin-watch)

To install Pest's "watch" plugin, you need to require the plugin via Composer.

```bash
composer require pestphp/pest-plugin-watch --dev
```

Make sure you also install [`fswatch`](https://github.com/emcrisostomo/fswatch#getting-fswatch) so Pest can monitor when a file changes.

Once both the plugin and `fswatch` are installed, you will be able to use the `--watch` option when running Pest. This option instructs Pest to monitor your application and automatically re-run your tests when you change files within a list of specified directories.

```bash
pest --watch
```

By default, the plugin monitors the following directories.

```plain
tests/
app/
src/
```

To customize the watched directories, supply a comma-separated list of directories (relative to your application root) to the `--watch` flag.

```bash
pest --watch=app,routes,tests
```

---

In this section, we have seen how plugins can enhance your Pest experience. Now, let's dive into architectural testing and how it can benefit your development process. By performing architectural testing, you can evaluate the overall design of your application and identify potential flaws before they become significant issues: [Arch Testing](/docs/arch-testing)
