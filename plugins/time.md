---
title: Time Plugin
description: The Time Plugin
---

# Snapshots Plugin

- [Overview](#overview)
- [Installation](#installation)
- [Usage](#usage)

<a name="overview"></a>
## Overview

The Time Plugin for Pest allows you to control the flow of time in your Pest tests. This plugin assumes you're using [Carbon](https://carbon.nesbot.com) to handle date and time in your app.

**Source code**: [github.com/spatie/pest-plugin-test-time](https://github.com/spatie/pest-plugin-test-time)

<a name="installation"></a>
### Installation

Install the Time Plugin via the Composer package manager:

```bash
composer require spatie/pest-plugin-test-time --dev
```

<a name="usage"></a>
## Usage

You can call `freeze` on the `testTime` function to freeze the current time.

```php
use Carbon\Carbon;
use function Spatie\PestPluginTestTime\testTime;

testTime()->freeze(); // the current time will not change anymore

Carbon::now(); // returns the time

sleep(2);

Carbon::now(); // will return the same time as above
```

### Freezing at a specific point in time

You can also freeze the time at a specific point by passing the time in format `Y-m-d H:i:s`.

```php
testTime()->freeze('2021-01-02 12:34:56');

\Carbon\Carbon::now()->format('Y-m-d H:i:s') // returns '2021-01-02 12:34:56';
```

### Changing the time

You can change the time, by calling any of the `add` and `sub` functions that are available on `Carbon`.

```php
testTime()->freeze('2021-01-02 12:34:56');

testTime()->addHour(); // time is now at '2021-01-02 13:34:56'

// you can even chain method calls
testTime()->subMinute()->addSeconds(2); // time is now at '2021-01-02 13:33:58'
```

---

Next section: [Watch Plugin →](/docs/plugins/watch)
