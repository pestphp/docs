---
title: Creating Plugins
description: Community plugins are essential for offering additional features to the Pest community, while the Pest team prioritizes keeping the framework's core small and fast. In this chapter, we'll discuss how to create your own plugins and share them with the community.
---

# Creating Plugins

Community plugins are essential for offering additional features to the Pest community, while the Pest team prioritizes keeping the framework's core small and fast. In this chapter, we'll discuss how to create your own plugins and share them with the community.

The simplest way to develop your own plugin is to begin with the [pest-plugin-template](https://github.com/pestphp/pest-plugin-template). To generate a new repository from the template repository, click on GitHub's "Use this template" button and name your new repository "pest-plugin-<yourpluginname>".

After cloning the repository, make sure to modify the "name" and "description" fields in the `composer.json` file to suit your plugin.

Pest plugins are capable of exposing custom test methods via the `$this` variable, adding namespaced functions, defining custom expectations, and offering custom CLI options.

## Adding Methods

Let's start building our plugin by making new test methods available via the `$this` variable. To accomplish this, define a PHP trait in your plugin.

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

In order to make this trait method invokable via Pest, we must inform Pest that it should make it available. This can be accomplished creating an `Autoload.php` file within your plugin with the following content.

```php
use YourGitHubUsername\PestPluginName\MyPluginTrait;

Pest\Plugin::uses(MyPluginTrait::class);
```

Lastly, we need to update our plugin's `composer.json` file to load our `Autoload.php` file as well as our plugin source code.

```json
"autoload": {
    "psr-4": {
        "YourGitHubUsername\\PestPluginName\\": "src/"
    },
    "files": ["src/Autoload.php"]
},
```

After you publish your plugin to [Packagist](https://packagist.org), users will be able to install your plugin via Composer. Once installed, they will be able to access your plugin's functions within their test closures.

```php
test('plugin example', function () {
    $this->myPluginMethod();

    //
})
```

## Adding Functions

A plugin can also define additional namespaced functions, which are typically declared within the plugin's `Autoload.php` file.

```php
namespace YourGitHubUsername\PestPluginName;

function myPluginFunction(): void
{
    //
}
```

Within your plugins functions, you can access the current `$this` variable that would typically be available to test closures by invoking the `test()` function with no arguments.

```php
namespace YourGitHubUsername\PestPluginName;

use PHPUnit\Framework\TestCase;

function myPluginFunction(): TestCase
{
    return test(); // Same as `return $this;`
}
```

Once you modify your plugin's `composer.json` file to autoload the `Autoload.php` file, users can easily access your function within their tests.

```php
use function YourGitHubUsername\PestPluginName\{myPluginFunction};

test('plugin example', function () {
    myPluginFunction();

    // ...
}
```

## Adding Custom Expectations

Custom expectations can be incorporated into your plugin's `Autoload.php` file. For information on how to build custom expectations, please refer to the comprehensive documentation on [Custom Expectations](/docs/custom-expectations).

---

As you can see, crafting plugins on Pest can serve as a fantastic starting point for your open-source endeavors! In the next chapter, we will explore the concept of "Higher Order Testing": [Higher Order Testing](/docs/higher-order-testing)
