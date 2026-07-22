---
title: Why Pest
description: Discover why Pest is the most elegant testing framework for PHP, designed to make the testing process enjoyable.
---

# Why Pest

When testing PHP code, you have access to a range of frameworks. However, we believe that Pest is the most elegant testing framework in the world. It is designed to make the testing process enjoyable, and our goal is to make your tests simple to read and understand, with a syntax that closely resembles natural human language:

```php
function sum($a, $b) {
    return $a + $b;
}

test('sum', function () {
  $result = sum(1, 2);

  expect($result)->toBe(3);
});
```

You may expect a seamless and efficient coding experience thanks to Pest's expressive API, inspired by Ruby's RSpec and Jest. In addition, the test reporting is well-organized, practical, and informative, with clear and concise error and stack trace displays for quick debugging. With Pest, you may obtain test reporting that is unmatched in its beauty, directly from the console:

<div class="terminal not-prose"><div class="l">&nbsp;&nbsp;<span class="chip fail">FAILED</span>&nbsp;&nbsp;<span class="white">Tests\Feature\ExampleTest</span> <span class="dim">></span> <span class="gray">it returns a successful response</span></div><div class="l">&nbsp;&nbsp;<span class="gray">Expected response status code [300] but received 200.</span></div><div class="l">&nbsp;</div><div class="l">&nbsp;</div><div class="l"><span class="dim">at</span> <span class="green">tests/Feature/ExampleTest.php:6</span></div><div class="cl"><span class="mk"></span><span class="ln">2</span><span class="src"></span></div><div class="cl"><span class="mk"></span><span class="ln">3</span><span class="src"> <span class="rose">it</span>(<span class="green">'returns a successful response'</span>, <span class="violet">function</span> () {</span></div><div class="cl"><span class="mk"></span><span class="ln">4</span><span class="src">     <span class="blue">$response</span> = <span class="blue">$this</span>-><span class="rose">get</span>(<span class="green">'/'</span>);</span></div><div class="cl"><span class="mk"></span><span class="ln">5</span><span class="src"></span></div><div class="cl"><span class="mk red b">→</span><span class="ln white b">6</span><span class="src">     <span class="blue">$response</span>-><span class="rose">assertStatus</span>(<span class="yellow">300</span>);</span></div><div class="cl"><span class="mk"></span><span class="ln">7</span><span class="src"> });</span></div><div class="cl"><span class="mk"></span><span class="ln">8</span><span class="src"></span></div><div class="l">&nbsp;</div><div class="l">&nbsp;</div><div class="l"><span class="gray">Tests:</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="red b">1 failed</span><span class="gray">, </span><span class="green b">1 passed</span> <span class="gray">(2 assertions)</span></div><div class="l"><span class="gray">Duration:</span>&nbsp;&nbsp;&nbsp;<span class="white">0.15s</span></div></div>

In addition to its beautiful test reporting, Pest also offers a range of other helpful features, including:

- Built-in [parallel](/docs/optimizing-tests#parallel) features for faster test runs
- [Browser Testing](/docs/browser-testing) for testing your application in various browsers and devices
- Beautiful [documentation](/docs/installation) that's easy to navigate
- Native [profiling tools](/docs/optimizing-tests#profiling) to optimize slow-running tests
- Out-of-the-box [Architectural Testing](/docs/arch-testing) to test application rules
- [Coverage](/docs/test-coverage) report directly on the terminal to track code coverage
- [Mutation Testing](#mutation-testing) to evaluate the quality of your test suite
- [Team Management](/docs/team-management) to manage tasks / todos with your team
- Dozens of [optional plugins](/docs/plugins), such as [Snapshot testing](https://github.com/spatie/pest-plugin-snapshots), to customize Pest to fit your needs

Whether you are engaged in a small personal project or a large-scale enterprise application, Pest has you covered. So, if you want to make the testing process enjoyable and efficient, give Pest a try. We are confident that you will love it as much as we do.

---

You may learn how to install Pest by visiting the next section of the documentation: [Installation →](/docs/installation)
