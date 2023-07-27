---
title: Installation
description: Installing Pest PHP Testing Framework is a simple process that can be completed in just a few steps.
---

# Installation

> **Requirements:** [PHP 8.1+](https://php.net/releases/)

Installing Pest PHP Testing Framework is a simple process that can be completed in just a few steps. Before you begin, make sure you have PHP `8.1+` or higher installed on your system.

**The first step** is to require Pest as a "dev" dependency in your project by running the following command on your command line.

```bash
composer require pestphp/pest --dev --with-all-dependencies
```

**Secondly**, you'll need to initialize Pest in your current PHP project. This step will create a configuration file named `Pest.php` at the root level of your test suite, which will enable you to fine-tune your test suite later.

```bash
./vendor/bin/pest --init
```

**Finally**, you can run your tests by executing the `pest` command.

```bash
./vendor/bin/pest
```

Here is an example of the output displayed when running Pest in a new, fresh project.

<div class="code-snippet">
    <img src="/assets/img/pestinstall.webp?1" style="--lines: 10" />
</div>

**Optionally**, if you are migrating from PHPUnit, you can use the `pest-plugin-drift` package to automatically convert your PHPUnit tests to Pest. For more information, check out the [Migrating from PHPUnit](/docs/migrating-from-phpunit-guide) guide.

---

After the installation process is finished, you can enhance your developer experience while working with Pest by configuring your editor: [Editor Setup →](/docs/editor-setup). If you're migrating from PHPUnit, check out the [Migration Guide →](/docs/migrating-from-phpunit-guide).
