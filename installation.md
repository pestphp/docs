---
title: Installation
description: Installation
---

# Installation

- [Requirements](#requirements)
- [Install Pest](#install-pest)

<a name="requirements"></a>

## Requirements

- [PHP 7.3+](https://php.net/releases/)
- [Composer](https://getcomposer.org/)
- [`Pest runs together with PHPUnit and it can be installed in any PHP project!`](/docs/compatibility)

<a name="install-pest"></a>

## Install

The installation process consists of 3 steps to be performed in your command line.

<br/>

``Step 1:`` Require Pest as a dev dependency in your project. Run:

```bash
composer require pestphp/pest --dev --with-all-dependencies
```

<hr/>

`Step 2:` What is your project's specification?  Select an option below to continue with the installation:

<details>
  <summary><em><b>Laravel Framework</b></em></summary>
    

  `2.1.` Require the [Laravel Plugin](/docs/plugins/laravel) via Composer, running the following command:

  ```bash
  composer require pestphp/pest-plugin-laravel --dev
  ```
  The dedicated Laravel Plugin gives you direct access to Laravel's testing API in your test files.


  `2.2.` Install Pest using Artisan, run the following command:

  ```bash
  php artisan pest:install
  ```

  Pest has now configured its [Files & Folders](/docs/files-and-folders) structure, and it’s ready to be used.

  <br/>

</details>

<details>
  <summary><em>Generic PHP Project or other PHP Framework</em></summary>
  
  `2.1.` Initialize and setup Pest. Run the following command:

  ```bash
  ./vendor/bin/pest --init
  ```

  `2.2.` Create an empty file `Pest.php` inside your `tests` directory.

  Pest has now configured its [Files & Folders](/docs/files-and-folders) structure, and it’s ready to be used.

</details>

<hr/>

`Step 3:` To run your tests, execute the command:

```bash
./vendor/bin/pest
```

<br/>

The image below shows the default Test Report in a brand-new project.

![Install](/assets/img/pestinstall.png)

---

**Next**: Let's see the syntax of a [Files & Folders →](/docs/files-and-folders)
