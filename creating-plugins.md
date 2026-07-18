---
title: Creating Plugins
description: In this chapter, we'll discuss how to create your own Pest plugins and share them with the community.
---

# Creating Plugins

Community plugins are essential for offering additional features to the Pest community, while the Pest team prioritizes keeping the framework's core small and fast. In this chapter, we'll discuss how to create your own plugins and share them with the community.

The simplest way to develop your own plugin is to begin with the [pest-plugin-template](https://github.com/pestphp/pest-plugin-template). To generate a new repository from the template, click GitHub's "Use this template" button and name your new repository "pest-plugin-<yourpluginname>".

Once you have cloned the repository, be sure to modify the "name" and "description" fields in the `composer.json` file to suit your plugin.

Pest plugins may expose custom test methods via the `$this` variable, add namespaced functions, define custom expectations, and offer custom CLI options.

## Adding Methods

Let's start building our plugin by making new test methods available via the `$this` variable. To accomplish this, define a PHP trait in your plugin:

```php
namespace YourGitHubUsername\PestPluginName;

trait MyPluginTrait
{
    public function myPluginMethod()
    {
        //
    }
}
```

To make this trait method invokable within tests, we must inform Pest that it should be made available. This may be accomplished by creating an `Autoload.php` file within your plugin with the following content:

```php
use YourGitHubUsername\PestPluginName\MyPluginTrait;

Pest\Plugin::uses(MyPluginTrait::class);
```

Finally, we need to update our plugin's `composer.json` file to load our `Autoload.php` file as well as our plugin's source code:

```json
"autoload": {
    "psr-4": {
        "YourGitHubUsername\\PestPluginName\\": "src/"
    },
    "files": ["src/Autoload.php"]
},
```

Once you publish your plugin to [Packagist](https://packagist.org), users will be able to install it via Composer. After installation, they will be able to access your plugin's functions within their test closures:

```php
test('plugin example', function () {
    $this->myPluginMethod();

    //
})
```

## Adding Functions

A plugin may also define additional namespaced functions, which are typically declared within the plugin's `Autoload.php` file:

```php
namespace YourGitHubUsername\PestPluginName;

function myPluginFunction(): void
{
    //
}
```

Within your plugin's functions, you may access the current `$this` variable that would typically be available to test closures by invoking the `test()` function with no arguments:

```php
namespace YourGitHubUsername\PestPluginName;

use PHPUnit\Framework\TestCase;

function myPluginFunction(): TestCase
{
    return test(); // Same as `return $this;`
}
```

Once you modify your plugin's `composer.json` file to autoload the `Autoload.php` file, users may access your function within their tests:

```php
use function YourGitHubUsername\PestPluginName\{myPluginFunction};

test('plugin example', function () {
    myPluginFunction();

    // ...
}
```

## Adding Custom Expectations

Custom expectations may be incorporated into your plugin's `Autoload.php` file. For information on how to build custom expectations, please refer to the documentation on [Custom Expectations](/docs/custom-expectations).

## Adding Arch Presets

If your plugin provides a custom Arch preset, you may define it within the `Autoload.php` file:

```php
pest()->preset('ddd', function () {
    return [
        expect('Infrastructure')->toOnlyBeUsedIn('Application'),
        expect('Domain')->toOnlyBeUsedIn('Application'),
    ];
});
```

Optionally, you may access the application's PSR-4 namespaces via the first argument of your closure's callback:

```php
pest()->preset('silex', function (array $userNamespaces) {
    dump($userNamespaces); // ['App\\']
});
```

---

As you can see, crafting plugins for Pest can serve as a helpful starting point for your open-source endeavors. In the next chapter, we will explore the concept of "Higher Order Testing": [Higher Order Testing](/docs/higher-order-testing)
