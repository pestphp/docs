---
title: Upgrade Guide
description: Upgrade Guide
---

# Upgrade Guide

## Upgrading To 2.x From 1.x

#### Estimated Upgrade Time: 1 Minute

> We attempt to document every possible breaking change. Since some of these breaking changes are in obscure parts of the framework, only a portion of these changes may actually affect your application.

### Updating Dependencies

Update your `pestphp/pest` dependency to `^2.0` in your `composer.json` file.

If you are using any official plugins, update them to `^2.0` in your `composer.json` file.

In 2.x, the `pestphp/pest-plugin-parallel` plugin has been removed and merged into the core of the framework.
Run `composer remove pestphp/pest-plugin-parallel` to remove it from your project. To continue using parallel
testing, you will need to install [Paratest](https://github.com/paratestphp/paratest/) as a dev dependency.
Run `composer require --dev brianium/paratest` to install it.

### Laravel Plugin

Previously, commands related to Laravel Artisan, such as "pest:install" and "pest:dataset," were part of the core of the pestphp/pest framework. However, they have now been extracted and moved to the Laravel Plugin to keep the framework's code as agnostic as possible. To use these commands, make sure you have the Laravel Plugin installed by running: `composer require pestphp/pest-plugin-laravel`.

## Upgrading To 1.x From 0.3

#### Estimated Upgrade Time: 1-2 Minutes

> We attempt to document every possible breaking change. Since some of these breaking changes are in obscure parts of the framework, only a portion of these changes may actually affect your application.

### Updating Dependencies

Update your `pestphp/pest` dependency to `^1.0` in your `composer.json` file.

If you are using any official plugins, update them to `^1.0` in your `composer.json` file.

## Upgrading To 0.3 From 0.2

#### Estimated Upgrade Time: 1-2 Minutes

> We attempt to document every possible breaking change. Since some of these breaking changes are in obscure parts of the framework, only a portion of these changes may actually affect your application.

### Updating Dependencies

Update your `pestphp/pest` dependency to `^0.3` in your `composer.json` file.

If you are using any official plugins, update them to `^0.3` in your `composer.json` file.

#### Global assertions

The usage of global assertions is deprecated in favor of the [expectation API](/docs/expectations/).

- Alternative 1: require global assertions plugin:
```bash
composer require pestphp/pest-plugin-global-assertions --dev
```

- Alternative 2: migrate to `$this` calls, or to [expectation API](/docs/expectations/):
```php
it('foo', function () {
    assertTrue(true); // won't work in 0.3

    // Replace by:
    $this->assertTrue(true);
    // or
    expect(true)->toBe(true);
});
```

## Upgrading To 0.2 From 0.1

#### Estimated Upgrade Time: 1-4 Minutes

> We attempt to document every possible breaking change. Since some of these breaking changes are in obscure parts of the framework, only a portion of these changes may actually affect your application.

### Updating Dependencies

Update your `pestphp/pest` dependency to `^0.2` in your `composer.json` file.

If you are using any official plugins, update them to `^0.2` in your `composer.json` file.

#### Namespaced Functions

Any functions provided by official plugins are no longer available globally. Therefore, you need to
import them. Here is an example:

```php
use function Pest\Laravel\get;

get('/')->assertSee('Laravel');
```

---

Next section: [Community →](/docs/community)
