---
title: Snapshot Testing
description: Snapshot testing is a convenient way to test your code by comparing a given expectation value against a previously stored snapshot of the same value.
---

# Snapshot Testing

Snapshot testing is a convenient way to test your code by comparing a given expectation value against a previously stored snapshot of the same value. This is helpful when you want to ensure that your code is not changing its output unexpectedly.

For example, let's imagine you have a string response coming from an API. You may use snapshot testing to ensure that the response is not changing unexpectedly:

```php
it('has a contact page', function () {
    $response = $this->get('/contact');

    expect($response)->toMatchSnapshot();
});
```

The first time you run this test, Pest will create a snapshot file — at `tests/.pest/snapshots` — with the response content. The next time you run the test, Pest will compare the response against the snapshot file. If the response is different, the test will fail. If the response is the same, the test will pass.

In addition, the given expectation value doesn't have to be a response; it may be anything. For example, you may snapshot an array:

```php
$array = /** Fetch array somewhere */;

expect($array)->toMatchSnapshot();
```

Of course, you may "rebuild" the snapshots at any time by using the `--update-snapshots` option:

```bash
./vendor/bin/pest --update-snapshots
```

## Handling Dynamic Data

Sometimes, the expected value may contain dynamic data that you cannot control, such as CSRF tokens in a form. In those cases, you may use [Expectation Pipes](/docs/custom-expectations#content-pipe-expectations) to replace that data. Here is an example:

```php
expect()->pipe('toMatchSnapshot', function (Closure $next) {
    if (is_string($this->value)) {
        $this->value = preg_replace(
            '/name="_token" value=".*"/',
            'name="_token" value="my_test"',
            $this->value
        );
    }

    return $next();
});
```

---

In this chapter, we've seen how powerful snapshot testing can be. Next, let's explore browser testing and how Pest can drive a real browser to test your application's user interface: [Browser Testing](/docs/browser-testing)
