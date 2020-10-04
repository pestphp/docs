---
title: Laravel Plugin
description: The Laravel Plugin
---

# Laravel Plugin

- [Overview](#overview)
	- [Installation](#installation)
- [Usage](#usage)
    - [Functions](#functions)
    - [Tests without description](#tests-without-description)

<a name="overview"></a>
## Overview

The Laravel Plugin for Pest gives your direct access to Laravel's testing API in your test files.

**Source code**: [github.com/pestphp/pest-plugin-laravel](https://github.com/pestphp/pest-plugin-laravel)


<a name="installation"></a>
### Installation

Install the plugin using Composer:

```bash
composer require pestphp/pest-plugin-laravel --dev
```

<a name="usage"></a>
## Usage

<a name="functions"></a>
### Functions

You may import and use the functions under the `Pest\Laravel` namespace
to have easy access Laravel's testing API. As example, the `get` function
makes a `GET` request into the application:

```php
use function Pest\Laravel\get;

it('has a welcome page', function () {
    get('/')->assertStatus(200);
});
```

<a name="tests-without-description"></a>
### Tests without description

Also with the Laravel plugin, tests can be written without description. This
lets you write even simpler tests than before:

```php
use function Pest\Laravel\get;

get('/')->assertStatus(200);
```

Note that Pest will automatically generate a description for these tests:

```bash
✓ get '/' → assertStatus 200
```

To import multiple functions, wrap them in curly brackets like so:

```php
use function Pest\Laravel\{get, getJson};

get('/')->assertStatus(200);

getJson('api/posts')->assertStatus(200);
```

Once again, Pest will generate a description for each test:

```bash
✓ get '/' → assertStatus 200
✓ getJson 'api/posts' → assertStatus 200
```

Remember, for the full list of available Laravel testing methods, please refer to the [Laravel documentation](https://laravel.com/docs/master/http-tests).

Next section: [Livewire →](/docs/plugins/livewire)
