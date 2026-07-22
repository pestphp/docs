---
title: Installation
description: Installing the Pest PHP testing framework is a simple process you may complete in a few steps.
---

# Installation

> **Note:** Pest requires [PHP 8.4+](https://php.net/releases/) to run.

Installing the Pest PHP testing framework is a simple process you may complete in a few steps. Before you begin, make sure you have PHP `8.4+` installed on your system.

First, require Pest as a "dev" dependency in your project by running the following commands on your command line:

```bash
composer remove phpunit/phpunit
composer require pestphp/pest --dev --with-all-dependencies
```

Next, you will need to initialize Pest in your current PHP project. This step will create a configuration file named `Pest.php` at the root level of your test suite, allowing you to fine-tune your test suite later:

```bash
./vendor/bin/pest --init
```

Finally, you may run your tests by executing the `./vendor/bin/pest` command:

```bash
./vendor/bin/pest
```

Here is an example of the output displayed when running Pest in a fresh project:

<div class="terminal not-prose"><div class="l"><span class="green">→</span> <span class="white">./vendor/bin/pest</span></div><div class="l">&nbsp;</div><div class="l">&nbsp;&nbsp;<span class="chip pass">PASS</span>&nbsp;&nbsp;<span class="white">Tests\Unit\ExampleTest</span></div><div class="l">&nbsp;&nbsp;<span class="green">✓</span> that true is true</div><div class="l">&nbsp;</div><div class="l">&nbsp;&nbsp;<span class="chip pass">PASS</span>&nbsp;&nbsp;<span class="white">Tests\Feature\ExampleTest</span></div><div class="l">&nbsp;&nbsp;<span class="green">✓</span> it returns a successful response</div><div class="l">&nbsp;</div><div class="l"><span class="gray">Tests:</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="green b">2 passed</span> <span class="gray">(2 assertions)</span></div><div class="l"><span class="gray">Duration:</span>&nbsp;&nbsp;&nbsp;<span class="white">0.09s</span></div></div>

If you are planning on using browser testing, you may install the `pest-plugin-browser` package to get started with browser testing in Pest. For more information, check out the [Browser Testing](/docs/browser-testing) documentation.

Optionally, if you are migrating from PHPUnit, you may use the `pest-plugin-drift` package to automatically convert your PHPUnit tests to Pest. For more information, check out the [Migrating from PHPUnit](/docs/migrating-from-phpunit-guide) guide.

---

After the installation process is finished, you may enhance your developer experience while working with Pest by configuring your editor: [Editor Setup →](/docs/editor-setup). If you are migrating from PHPUnit, check out the [Migration Guide →](/docs/migrating-from-phpunit-guide).
