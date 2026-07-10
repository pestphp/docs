---
title: Installation
description: Installing the Pest PHP testing framework is a simple process you may complete in a few steps.
---

# Installation

> **Requirements:** [PHP 8.4+](https://php.net/releases/)

Installing the Pest PHP testing framework is a simple process you may complete in a few steps. Before you begin, make sure you have PHP `8.4+` installed on your system.

**First**, require Pest as a "dev" dependency in your project by running the following commands on your command line:

```bash
composer remove phpunit/phpunit
composer require pestphp/pest --dev --with-all-dependencies
```

**Next**, you will need to initialize Pest in your current PHP project. This step will create a configuration file named `Pest.php` at the root level of your test suite, allowing you to fine-tune your test suite later:

```bash
./vendor/bin/pest --init
```

**Finally**, you may run your tests by executing the `pest` command:

```bash
./vendor/bin/pest
```

Here is an example of the output displayed when running Pest in a fresh project:

<div class="code-snippet">
    <img src="/assets/img/pestinstall.webp?1" style="--lines: 10" />
</div>

If you are planning on using Browser Testing, you may install the `pest-plugin-browser` package to get started with browser testing in Pest. For more information, check out the [Browser Testing](/docs/browser-testing) documentation.

**Optionally**, if you are migrating from PHPUnit, you may use the `pest-plugin-drift` package to automatically convert your PHPUnit tests to Pest. For more information, check out the [Migrating from PHPUnit](/docs/migrating-from-phpunit-guide) guide.

---

After the installation process is finished, you may enhance your developer experience while working with Pest by configuring your editor: [Editor Setup →](/docs/editor-setup). If you are migrating from PHPUnit, check out the [Migration Guide →](/docs/migrating-from-phpunit-guide).
