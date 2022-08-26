---
title: Write a Test
description: Write a Test
---
# Write a Test

- [Writing a Test](#writing-a-test)
- [Understanding the Test](#understanding-the-test)

<a name="writing-a-test"></a>
## Writing a Test

To write a Pest Test in Laravel Framework, you just have to run the command:

```shell
php artisan make:test HomePageTest --pest
```

Artisan will create a file called `HomePageTest.php` inside your `tests/Feature/` folder.

<a name="understanding-the-test"></a>
## Understanding the Test


```php
<?php

test('example', function () {
    $response = $this->get('/');
    $response->assertStatus(200);
});
```

Let's have a look on what it actually does...

- The test above makes a request to your web server to verify if your home page loads correctly.
- A web server will reply with the HTTP status `200`, indicating that the request could be processed without errors.
- The method `assertStatus()` will be verifying that the code was given is equal `200` (OK).

Now, let's write a test which goes to a page that does not exist and let's try to assert the `200` status.

```php
<?php

test('page does not exist', function () {
    $response = $this->get('/page-doesnt-exist');
    $response->assertStatus(200);
});
```

The test above will fail with the message: `Expected response status code [200] but received 404.`.

This was expected because a page that does not exist will return the famous code `404` and we asked to check for `200`.

Let's fix this test making it pass:

```php
<?php

test('page does not exist', function () {
    $response = $this->get('/page-doesnt-exist');
    $response->assertStatus(404);
});
```

Now, the test Passes. It confirms that the page does not exist.


---

**Next**: Pest [`Expectations` →](/docs/wriexpectations)