---
title: Creating Plugins
description:
---

# Creating Plugins

Community plugins are essential for offering optional features to the community, while we prioritize keeping the framework's core small and fast. In this chapter, we'll provide documentation on how to create your own plugins and share them with the community.

The simplest way to develop your own plugin is to begin with the [pest-plugin-template](https://github.com/pestphp/pest-plugin-template). To generate a new repository from it, click on the "Use this template" button and name it "pest-plugin-<yourpluginname>".

After cloning the repository, make sure to modify the "name" and "description" fields in the `composer.json` file to suit your plugin.

In general, plugins are capable of introducing fresh test methods into the `$this` closure, namespaced functions, custom expectations, or custom CLI options.

## Adding methods

Let's start by adding test methods into the `$this` closure. For that, simply create a `trait` in your plugin

```php
namespace YourGithubUsername\PestPluginName;

trait MyPluginTrait
{
    public function myPluginMethod()
    {
        //
    }
}
```

In order to make the functionality accessible, we must inform Pest that it should utilize it. This can be accomplished creating an `Autoload.php` with the following content.

```php
use YourGithubUsername\PestPluginName\MyPluginTrait;

Pest\Plugin::uses(MyPluginTrait::class);
```

Lastly, we must load the files. To do this, we modify our `composer.json` file as follows. We will reference our namespace for PSR-4 autoloading and instruct it to load and execute our `Plugin.php` file:

```json
"autoload": {
    "psr-4": {
        "YourGithubUsername\\PestPluginName\\": "src/"
    },
    "files": ["src/Autoload.php"]
},
```

Naturally, once users require your plugin, they will be able to access your plugin's functions within their test closures.

```php
test('plugin example', function () {
    $this->myPluginMethod();

    //
})
```

## Adding functions

A plugin can integrate additional namespaced functions, which should typically are declared on the plugin's `Autoload.php` file.

```php
namespace YourGithubUsername\PestPluginName;

function myPluginFunction(): void
{
    //
}
```

Please note that within functions, you can access the current `$this` closure variable by utilizing the `test()` function with no arguments.

```php
namespace YourGithubUsername\PestPluginName;

use PHPUnit\Framework\TestCase;

function myPluginFunction(): TestCase
{
    return test(); // same as `return $this;`
}
```

Once you modify the `composer.json` file to autoload the Autoload.php file, users can access your namespaced function in the following manner.

```php
use function YourGithubUsername\PestPluginName\{myPluginFunction};

test('plugin example', function () {
    myPluginFunction();

    // ...
}
```

## Adding custom expectations

Custom expectations can be incorporated into the plugin's `Autoload.php` file. For further information on how to "define" custom expectations, please refer to the chapter on [Custom Expectations](/docs/custom-expectations].

---

As you can see, crafting plugins on Pest is simple and uncomplicated, and it can serve as a fantastic starting point for your open-source endeavors! In the next chapter, we will explore the concept of "Higher Order Testing": [Higher Order Testing](/docs/higher-order-testing)
