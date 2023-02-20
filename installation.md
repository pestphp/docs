---
title: Installation
description: Installation
---

# Installation

> **Requirements:** [PHP 8.1+](https://php.net/releases/)

Installing Pest PHP Testing Framework is a simple process that can be completed in just a few steps. Before you begin, make sure you have PHP `8.1+` or higher installed on your system.

**The first step** is to require Pest as a dev dependency in your project by running the following command in your command line:

```bash
composer require pestphp/pest --dev --with-all-dependencies
```

**In the second step**, you'll need to to initialize Pest in your current PHP project through the following command:

```bash
./vendor/bin/pest --init
```

A `Pest.php` configuration file will be generated at the root level of your test suite, allowing you to configure your test suite at a later time.

**In the final step**, you can run your tests by executing the command:

```bash
./vendor/bin/pest
```

The default Test Report in a brand-new project is shown in the image below.

![Install](/assets/img/pestinstall.png)

---

Once the installation process is complete, you can learn more about the syntax of files and folders visiting the next section of the documentation: [Files And Folders →](/docs/files-and-folders)
