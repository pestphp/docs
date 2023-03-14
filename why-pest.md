---
title: Why Pest
description: Welcome to the Pest Testing Framework!
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

You can expect a smooth and efficient coding experience thanks to its easy-to-use API inspired by Ruby's Rspec and Jest. Writing tests with Pest is simple, and the test reporting is well-organized, practical, and informative, with clear and concise error and stack trace displays for quick debugging. With Pest, you can obtain test reporting that is unmatched in its beauty, directly from the console! Furthermore, its easily readable error messages and stack traces enable you to debug with lightning-fast speed.

Aside from its exceptional test reporting, Pest also boasts an array of other valuable features, including testing coverage, profiling, and more. Whether you're engaged in a small personal project or a large-scale enterprise application, Pest has got you covered.

So if you want to make the testing process enjoyable and efficient, give Pest a try. We're confident that you'll love it as much as we do.

---

You can learn about how to install Pest by visiting the next section of the documentation: [Installation →](/docs/installation)
