---
title: Type Coverage
description: Type Coverage is a metric used to measure the percentage of code that is covered by type declarations
---

# Snapshot Testing

Snapshot Testing is a great way to test your code by comparing by the given expectation value to a previously stored snapshot of the same value. This is useful when you want to ensure that your code is not changing its output unexpectedly.

As example, let's say you have a string response coming from an API. You can use snapshot testing to ensure that the response is not changing unexpectedly.

```php
it('has a contact page', function () {
    $response = $this->get('/contact');

    expect($response)->toMatchSnapshot();
});
```

The first time you run this test, it will create a snapshot file - at `tests/.pest/snapshots` - with the response content. The next time you run the test, it will compare the response with the snapshot file. If the response is different, the test will fail. If the response is the same, the test will pass.

In addition, the given expectation value doesn't have to be a response; it can be anything. For example, you can snapshot an array:

```php
$array = /** Fetch array somewhere */;

expect($array)->toMatchSnapshot();
```

And of course, you can "rebuild" the snapshots at any time by using the `--update-snapshots` option:

```bash
./vendor/bin/pest --update-snapshots
```

---

In this chapter, we've seen how powerful snapshot testing is. In the following chapter, we will dive into Pest's custom helpers: [Custom Helpers](/docs/custom-helpers)
