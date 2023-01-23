---
title: About
description: About
---

# About

Welcome to the Pest Testing Framework! Pest is an elegant and sophisticated PHP testing framework that is designed to make the testing process enjoyable. Our goal is to make tests easy to read and understand, with a code syntax that closely resembles natural human language.

With Pest, you can expect a smooth and efficient coding experience thanks to its functional and declarative code style. Writing tests with Pest is simple, and the test reporting is well-organized, practical, and informative, with clear and concise error and stack trace displays for quick debugging.

Here is an example of a test written in Pest:

```php
function sum($a, $b) {
    return $a + $b;
}

test('sum', function () {
  expect(sum(1, 2))->toBe(3);
});
```

As you can see, the test is easy to read and understand, and the code syntax closely resembles natural human language. This makes it easy for developers to write tests, even if they are new to testing.

Pest also provides a variety of other features, such as testing coverage, easy-to-use mocking tools, and more. Whether you are working on a small personal project or a large enterprise application, Pest has you covered.

So if you want to make the testing process enjoyable and efficient, give Pest a try. We're confident that you'll love it as much as we do.

---

You can learn about how to install Pest by visiting the next section of the documentation: [Installation →](/docs/installation)
