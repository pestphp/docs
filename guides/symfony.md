---
title: Symfony Guide
description: The Symfony Guide
---

# Symfony Guide

Pest was built to be framework agnostic, but as it's easily extendable, it works well in [Symfony](https://symfony.com) applications.

Now, in this guide, we are going to transform a Symfony test into a Pest test.

First, create the `tests/Pest.php` file, and add the following code:

```php
<?php

use Symfony\Bundle\FrameworkBundle\Test\WebTestCase;

uses(WebTestCase::class)->in('Controller');
```

This tells Pest that all tests under the `Controller` directory will use the `WebTestCase`.

A basic functional test in Symfony looks like the following:

```php
<?php

namespace App\Tests\Controller;

use Symfony\Bundle\FrameworkBundle\Test\WebTestCase;

class DefaultControllerTest extends WebTestCase
{
    public function testSomething()
    {
        $client = static::createClient();
        $client->request('GET', '/');

        $this->assertResponseIsSuccessful();
        $this->assertSelectorTextContains('h1', 'Hello World');
    }
}
```

With Pest, the same test could be written like this:

```php
<?php
// tests/Controller/DefaultControllerTest.php

test('something', function() {
    static::createClient()->request('GET', '/');
    $this->assertResponseIsSuccessful();
    $this->assertSelectorTextContains('h1', 'Hello World');
});
```

> **Note:** If you have set up your project using [symfony skeleton](https://github.com/symfony/skeleton), be sure that you have [BrowserKit component](https://symfony.com/doc/current/components/browser_kit.html) installed, so that you can successfully run the tests that interact with requests.

Next section: [Pest From Scratch →](/docs/guides/pest-from-scratch)
