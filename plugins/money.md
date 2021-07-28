---
title: Money Plugin
description: The Money Plugin
---

# Money Plugin

- [Overview](#overview)
- [Installation](#installation)
- [Usage](#usage)

<a name="overview"></a>
## Overview

The Money Plugin for Pest provides expectations for working with popular PHP money libraries. Currently, this plugin
provides support for the following money libraries:

- [Brick](https://github.com/brick/money)
- [MoneyPhp](https://github.com/moneyphp/money)

**Source code**: [https://github.com/lukeraymonddowning/pest-plugin-money](https://github.com/lukeraymonddowning/pest-plugin-money)

<a name="installation"></a>
## Installation

Install the Money Plugin via the Composer package manager:

```bash
composer require lukeraymonddowning/pest-plugin-money --dev
```

<a name="usage"></a>
## Usage

### toBeMoney

To simply assert that an object is a monetary value, use the `toBeMoney` method:

```php
expect(Money::of(100, "GBP"))->toBeMoney();
expect("Hello World")->not->toBeMoney();
```

### toCost

To check that a monetary value is equal to a certain amount, use the `toCost` method:

```php
expect(Money::of(150, "GBP"))->toCost(150, 'GBP');
expect(Money::of(150, "GBP"))->toCost($anotherMoneyObject);
expect(Money::of(150, "GBP"))->not->toCost(100, 'GBP');
```

### toCostLessThan

To check that a monetary value is less than a certain amount, use the `toCostLessThan` method:

```php
expect(Money::of(150, "GBP"))->toCostLessThan(160, 'GBP');
expect(Money::of(150, "GBP"))->toCostLessThan($anotherMoneyObject);
expect(Money::of(150, "GBP"))->not->toCostLessThan(140, 'GBP');
```

### toCostMoreThan

To check that a monetary value is more than a certain amount, use the `toCostMoreThan` method:

```php
expect(Money::of(150, "GBP"))->toCostMoreThan(140, 'GBP');
expect(Money::of(150, "GBP"))->toCostMoreThan($anotherMoneyObject);
expect(Money::of(150, "GBP"))->not->toCostMoreThan(160, 'GBP');
```

## Choosing a money library

This package will attempt to autodetect which of the supported money libraries you have installed. If you want to
enforce which library to use, you can call the `useMoneyLibrary` function. Pass the class name of the relevant money package:

```php
useMoneyLibrary(\Money\Money::class); // Use the MoneyPHP library
useMoneyLibrary(\Brick\Money\Money::class); // Use the Brick Money library
```

## Setting a default currency

If your application primarily uses a single currency, it can be annoying having to declare it as the second
argument for each expectation. By setting a default, you can omit the currency and just provide the amount:

```php
useCurrency('GBP'); 
expect($money)->toCost('100'); // Uses Great British Pounds

useCurrency('USD'); 
expect($money)->toCost('100'); // Uses US Dollars
```

---

Next section: [Mutation Testing Plugin →](/docs/plugins/mutation-testing)
