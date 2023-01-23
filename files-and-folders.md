---
title: Files & Folders
description: Pest files and folders structure
---

# Files & Folders

- [Overview](#overview)
- [Structure](#structure)

<a name="overview"></a>

## Overview

This section provides an overview of what each file and folder represents. If you are familiar with PHPUnit, you will notice that the file and folder structure of Pest is similar.

After [installing](/docs/installation) Pest, you will have the following files and folders:

#### Laravel:

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

#### Generic PHP projects

```plain
├── 📂 tests
│   └── Pest.php
│   └── ExampleTest.php
```

<a name="structure"></a>

## Structure

**Folder tests**: Contains everything related to your tests.

**Unit and Feature Folders**: These are where your test logic lives. You are welcome to modify and rename this folders according to your project convention.

**Unit**: Unit tests are designed to test a small portion of your isolated code. These tests should not depend on external resources like database or APIs.

A good example is testing the `calcTotalPrice()` method to guarantee it returns the correct sum of given values. This test has no external dependencies. In other words, you don't need an existing customer, a valid product or an instance of an invoice to write this test.

**Feature**: Features tests, in the other hand, are testing if parts of your code behave as expected when acting together.
Following the previous example, a Feature Test would verify that the `ProductCart` class is passed to the `CheckOut` class and it has a proprety `totalAmount`, and that its value equals the sum of each product's price.

**ExampleTest.php**: Pest will create an Example Test during its installation. Laravel users, may find one example test for a Feature Test and another for Unit tests.

**TestCase.php**: The TestCase file is responsible for bootstraping your application. When using Laravel case, it provides access to framework resources such as  Facades and Helpers. Read more in [Underlying Test Case](/docs/underlying-test-case).

**Pest.php**: This is the entry point of Pest PHP. Pest autoloads this file and here can reference your [Test Case](/docs/underlying-test-case), create [Custom Helpers](/docs/helpers) and extend Pest Expectation API with [Custom Expectations](/docs/custom-expectations).

**phpunit.xml**: The files **phpunit.xml** or **phpunit.dist.xml** contains the PHPUnit configuration.

---

In the next section, you can learn about how to write a test with Pest: [Writing Tests →](/docs/writing-tests)
