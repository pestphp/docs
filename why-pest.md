---
title: Why Pest
description: When testing PHP code, you have access to a range of frameworks. Nevertheless, we believe that Pest is the most elegant and sophisticated testing framework in the world. It's designed to make the testing process enjoyable, and our goal is to make tests easy to read and understand, with a code syntax that closely resembles natural human language.
---

# Why Pest

When testing PHP code, you have access to a range of frameworks. Nevertheless, we believe that Pest is the most elegant and sophisticated testing framework in the world. It's designed to make the testing process enjoyable, and our goal is to make tests easy to read and understand, with a code syntax that closely resembles natural human language.


```php
function sum($a, $b) {
    return $a + $b;
}

test('sum', function () {
  $result = sum(1, 2);

  expect($result)->toBe(3);
});
```

You can expect a smooth and efficient coding experience thanks to its easy-to-use API inspired by Ruby's Rspec and Jest. In addition, the test reporting is well-organized, practical, and informative, with clear and concise error and stack trace displays for quick debugging. With Pest, you can obtain test reporting that is unmatched in its beauty, directly from the console!

<div >
    <img src="/assets/img/failure.webp" style="display: block; margin-left: auto; margin-right: auto;" width="80%" height="600" />
</div>

In addition to its exceptional test reporting, Pest also offers a range of other valuable features, including:

- Built-in [Parallel](/docs/optimizing-tests#parallel) Features for faster test runs
- Beautiful [documentation](/docs/installation) that's easy to navigate
- Native [profiling tools](/docs/optimizing-tests#profiling) to optimize slow-running tests
- Out-of-the-box [Architectural](/docs/arch-testing) Testing to test application rules
- [Coverage](/docs/test-coverage) report directly on the terminal to track code coverage
- Dozens of [optional plugins](/docs/plugins), such as Watch Mode and Snapshot testing, to customize Pest to fit your needs.

Whether you're engaged in a small personal project or a large-scale enterprise application, Pest has got you covered. So if you want to make the testing process enjoyable and efficient, give Pest a try. We're confident that you'll love it as much as we do.

---

You can learn about how to install Pest by visiting the next section of the documentation: [Installation →](/docs/installation)
