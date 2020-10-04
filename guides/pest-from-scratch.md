---
title: Pest From Scratch Guide
description: How to Install Pest on a Blank Project
---

# Pest From Scratch

- [Installation](#installation)
    
<a name="installation"></a>
### Installation

To install Pest on a new blank project, you just have to follow a few simple steps:

> **Note:** Pest requires [PHP 7.3+](https://php.net/releases/) to work.

1. Inside a new directory, set up your project with composer:

```bash
composer init
```

2. Then, make sure your new `composer.json` file contains the following options:

```json
"minimum-stability": "dev",
"prefer-stable": true,
```

3. Now, require **Pest**:

```bash
composer require pestphp/pest --dev
```

4. Use the `pest --init` command to initialise your test environement:

```bash
./vendor/bin/pest --init
```

Under the hood, this Pest command will create the following:

- a `tests` directory,
- a `phpunit.xml` file,
- a `tests/Pest.php` file,
- a `tests/Helpers.php` file,
- a `tests/ExampleTest.php` file,

5. Finally, you can run Pest directly from the command line:

```bash
./vendor/bin/pest
```

![Install](/assets/img/pestinit.png)

Happy Testing!

Next section: [Creating Plugins →](/docs/guides/plugins)
