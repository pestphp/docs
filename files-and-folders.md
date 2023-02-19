---
title: Files & Folders
description: Pest files and folders structure
---

# Files & Folders

This section is intended to provide you with a brief overview of the files and folders that you will encounter when using Pest PHP. Upon successful [installation](/docs/installation) of Pest, you will find the following files and folders in your project:

```plain
├── 📂 tests
│   ├── 📂 Unit
│   │   └── ExampleTest.php
│   └── 📂 Feature
│   │   └── ExampleTest.php
│   └── TestCase.php
│   └── Pest.php
├── phpunit.xml
```

The `tests` folder serves as the main directory where all your test files will reside. Within this folder, you will find two sub-folders, `Unit` and `Feature`, which house your unit and feature tests, respectively. The `TestCase.php` file is where you can define common functionality or setup that you want to use across all your tests. Lastly, the `Pest.php` file is where you can configure your test suite, as mentioned earlier.

Additionally, the `phpunit.xml` file can be found in your project, and is used to configure PHPUnit's various options when running tests. It's important to note that Pest is built on top of PHPUnit, which means that all of the options offered by PHPUnit can also be used in Pest. Therefore, any customization or configuration that you do with the `phpunit.xml` file will also apply to Pest tests.

---

In the next section, you can learn about how to write a test with Pest: [Writing Tests →](/docs/writing-tests)
