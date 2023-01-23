---
title: Installation
description: Installation
---

# Installation

> **Requirements:** [PHP 8.1+](https://php.net/releases/).

Installing Pest PHP Testing Framework is a simple process that can be completed in just a few steps. Before you begin, make sure you have PHP `8.1+` or higher installed on your system.

**The first step** is to require Pest as a dev dependency in your project by running the following command in your command line:

```bash
composer require pestphp/pest --dev --with-all-dependencies
```

**In the second step**, you'll need to determine the specification of your project:

- If you're using the Laravel Framework, you'll need to require the Laravel Plugin via Composer by running the following command:
```bash
composer require pestphp/pest-plugin-laravel --dev
php artisan pest:install
```

- If you're working on a Generic PHP Project or other PHP Framework, the process is slightly different. In this case, you'll need to initialize and setup Pest by running the following command:
```bash
./vendor/bin/pest --init
```

**In the final step**, you can run your tests by executing the command:

```bash
./vendor/bin/pest
```

The default Test Report in a brand-new project is shown in the image below.

![Install](/assets/img/pestinstall.png)

---

Once the installation process is complete, you can learn more about the syntax of files and folders visiting the next section of the documentation: [Files And Folders →](/docs/files-and-folders)
