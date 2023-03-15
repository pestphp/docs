---
title: Plugins
description: In this section, we will discuss the official and community plugins that we endorse. It's a common misconception among Pest users that plugins are necessary for Pest to integrate smoothly with their frameworks. However, this is not the case. Plugins primarily offer namespaced functions, console commands, and additional options.
---

# Plugins

In this section, we will discuss the official and community plugins that we endorse. It's a common misconception among Pest users that plugins are necessary for Pest to integrate smoothly with their frameworks. However, this is not the case. Plugins primarily offer namespaced functions, console commands, custom expectations, and additional command-line options.

If you are a plugin developer, please consult the [Creating Plugins](/docs/creating-plugins) section for more information on how to create Pest plugins.

Here is a comprehensive list of plugins that are officially maintained by the Pest team:

<div class="collection-method-list" markdown="1">

- [Faker](#faker)
- [Laravel](#laravel)
- [Livewire](#livewire)
- [Watch](#watch)

</div>

Here is a comprehensive list of plugins that are officially maintained by the Pest team. Although we have provided a comprehensive list of community plugins available for Pest, please note that there are many other community plugins that you can discover on GitHub.

<div class="collection-method-list" markdown="1">

- [Money](#money)
- [Snapshots](#snapshots)

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
use function Pest\Faker\fake;

it('generates a portuguese name', function () {
    $name = fake('pt_PT')->name; // Nuno Maduro

    //
});
```

To obtain additional information on Faker, including comprehensive details about the API it provides, please consult the [documentation for Faker PHP](https://fakerphp.github.io/).

---

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

<a name="watch"></a>
## Watch

**Source code**: [github.com/pestphp/pest-plugin-watch](https://github.com/pestphp/pest-plugin-watch)

To get started, you need to require this plugin via Composer.

```bash
composer require pestphp/pest-plugin-watch --dev
```

After the installation process is finished, you will have access to the --watch console option, which monitors your application and automatically runs your Pest tests when you make changes to files within specified directories.

```bash
pest --watch
```

The Watch plugin, as a default setting, monitors the following directories.

```
tests/
app/
src/
```

To monitor a customized set of directories, simply supply a comma-separated list to the `--watch` flag.

```bash
pest --watch=app,routes,tests
```

---

<a name="money"></a>
## Money

**Source code**: [github.com/lukeraymonddowning/pest-plugin-money](https://github.com/lukeraymonddowning/pest-plugin-money)

To get started, you need to require this plugin via Composer.

```bash
composer require lukeraymonddowning/pest-plugin-money --dev
```

Once you have completed the installation process, you may choose to make use of the plugin's tailored expectations to test widely-used money libraries. The plugin currently offers support for [Brick](https://github.com/brick/money) and [MoneyPhp](https://github.com/moneyphp/money).

You can use the `toBeMoney` method to make a straightforward assertion that an object represents a monetary value.

```php
expect(Money::of(100, 'GBP'))->toBeMoney();
expect('Hello World')->not->toBeMoney();
```

If you want to verify that a monetary value is equivalent to a specific amount, utilize the `toCost` method.

```php
expect(Money::of(150, 'GBP'))->toCost(150, 'GBP');
expect(Money::of(150, 'GBP'))->toCost($anotherMoneyObject);
expect(Money::of(150, 'GBP'))->not->toCost(100, 'GBP');
```

If you need to confirm that a monetary value is lower than a particular amount, use the `toCostLessThan` method.

```php
expect(Money::of(150, 'GBP'))->toCostLessThan(160, 'GBP');
expect(Money::of(150, 'GBP'))->toCostLessThan($anotherMoneyObject);
expect(Money::of(150, 'GBP'))->not->toCostLessThan(140, 'GBP');
```

If you want to verify that a monetary value exceeds a particular amount, make use of the `toCostMoreThan` method.

```php
expect(Money::of(150, 'GBP'))->toCostMoreThan(140, 'GBP');
expect(Money::of(150, 'GBP'))->toCostMoreThan($anotherMoneyObject);
expect(Money::of(150, 'GBP'))->not->toCostMoreThan(160, 'GBP');
```

The package will automatically try to detect the supported money library you have installed. However, if you want to specify a particular library to use, you can invoke the `useMoneyLibrary` function and provide the class name of the relevant money package.

```php
useMoneyLibrary(\Money\Money::class); // Use the MoneyPHP library
useMoneyLibrary(\Brick\Money\Money::class); // Use the Brick Money library
```

If your application primarily deals with a single currency, it can be inconvenient to specify it as the second argument for every expectation. To avoid this, you can establish a default value and only provide the amount without the currency.

```php
useCurrency('GBP');
expect($money)->toCost('100'); // Uses Great British Pounds

useCurrency('USD');
expect($money)->toCost('100'); // Uses US Dollars
```

---

<a name="snapshots"></a>
## Snapshots

**Source code**: [github.com/spatie/pest-plugin-snapshots](https://github.com/spatie/pest-plugin-snapshots)

To get started, you need to require this plugin via Composer.

```bash
composer require spatie/pest-plugin-snapshots --dev
```

Snapshot testing is an extremely helpful technique when you want to ensure that things don't unexpectedly change. You can discover additional information about snapshot testing at this link: [sebastiandedeyne.com/a-package-for-snapshot-testing-in-phpunit/](https://sebastiandedeyne.com/a-package-for-snapshot-testing-in-phpunit).

The `assertMatchesSnapshot()` function validates that the provided string matches the current snapshot.

```php
use function Spatie\Snapshots\assertMatchesSnapshot;

it('renders correctly', function () {
    $html = $this->get('/')->getContent();

    assertMatchesSnapshot($html);
});
```

In addition, if you anticipate a modified value, it may be necessary to utilize the -d or --update-snapshots flag to update the current snapshots.

```bash
./vendor/bin/pest -d --update-snapshots
```

If you're dealing with particular data formats like JSON or XML, it's recommended to use a specialized `assertMatchesJsonSnapshot()` or `assertMatchesXmlSnapshot()` method. These methods save snapshots as .json or .xml files, respectively, and generate a more informative diff when the snapshot doesn't match.

```bash
assertMatchesSnapshot()
assertMatchesFileHashSnapshot()
assertMatchesFileSnapshot()
assertMatchesHtmlSnapshot()
assertMatchesJsonSnapshot()
assertMatchesObjectSnapshot()
assertMatchesTextSnapshot()
assertMatchesXmlSnapshot()
assertMatchesYamlSnapshot()
```

---

In this section, we have seen how plugins can enhance your Pest experience. Now, let's dive into architectural testing and how it can benefit your development process. By performing architectural testing, you can evaluate the overall design of your application and identify potential flaws before they become significant issues: [Arch Testing](/docs/arch-testing)
