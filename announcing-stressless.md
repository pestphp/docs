---
title: Announcing Stressless
description: "We are thrilled to announce the release of a brand new plugin for Pest PHP: Stressless - it brings the power of stress testing to the PHP ecosystem."
---

# Announcing Stressless

We are thrilled to announce the release of a brand new plugin for Pest PHP: **[Stressless](/docs/stress-testing)**.

It's a fresh new addition to the Pest PHP family, and it brings the power of stress testing to the PHP ecosystem. It integrates seamlessly with Pest PHP, combining the power of stress testing with the simplicity and elegance of Pest's Expectation API.

Check out this YouTube video where we walk you through the installation and setup of the Stressless plugin:

<iframe width="560" height="315" src="https://www.youtube.com/embed/SaMoPZwdOCY?si=KBskkVWLUUSyK0u0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

As you can see, it's effortless to get started with Stressless — all you need to do is require the package using Composer, and you're ready to go!

There are two main ways to use Stressless. You may use it to quickly stress test your application from the command line:

```bash
./vendor/bin/pest stress example.com --concurrency=5 --duration=10
````

Or you can use it to write stress tests in your Pest PHP test files:

```php
<?php

test('black friday', function () {
    $result = stress('example.com')
        ->concurrently(5)
        ->for(10)->seconds();

    $requests = $result->requests;

    expect($requests->failed->count)
        ->toBe(0);

    expect($requests->duration->med)
        ->toBeLessThan(100.0); // 100ms
});
```

Check our documentation to get started with Stress Testing / Stressless: **[Stress Testing →](/docs/stress-testing)**. We hope you enjoy this new addition to the Pest PHP family!

---

If you're considering a testing framework for your next project, here's why you should give Pest a try: [Why Pest →](/docs/why-pest)
