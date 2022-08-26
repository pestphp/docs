---
title: Global Assertions Plugin
description: A plugin that adds global assertions back to Pest
---

# Global Assertions Plugin

- [Overview](#overview)
- [Installation](#installation)
- [Available Functions](#available-functions)

<a name="overview"></a>
## Overview

The Global Assertions Plugin for Pest provides global functions for assertions that were removed in v0.3

**Source code**: [github.com/pestphp/pest-plugin-global-assertions](https://github.com/pestphp/pest-plugin-global-assertions)

<a name="installation"></a>
## Installation

Install the Global Assertions Plugin via the Composer package manager:

```bash
composer require pestphp/pest-plugin-global-assertions --dev
```

<a name="available-functions"></a>
## Available functions

This allows you to do the following, without calling the `$this` object. For a full list of available methods, please refer to [the compiled list](https://github.com/pestphp/pest-plugin-global-assertions/blob/main/src/compiled.php).

```php
it('asserts that TRUE is TRUE using global function', function () {
    assertTrue(true);
});
```

This plugin is a good temporary helper when upgrading to [the Expectations API](/docs/expectations) in Pest v0.3

---

Next section: [Snapshots →](/docs/plugins/snapshots)