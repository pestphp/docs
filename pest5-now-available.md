---
title: Pest 5 Now Available
description: Today, we're thrilled to announce the release of Pest 5. Built on PHP 8.4 and PHPUnit 13, Pest 5 introduces Test Impact Analysis, the Agent Browser, AI Evals, a first-party PHPStan plugin, automated refactoring with Rector, time-balanced sharding, and much more.
---

# Pest 5 Now Available

After shipping Pest 4, with the best browser testing in the world, we honestly thought a release as big as Pest 4 simply wouldn't be possible again.

And yet — yet — today I'm proud to introduce you to the biggest release of Pest yet: **Pest 5**.

Where Pest 4 was all about **real browser testing**, Pest 5 is about a test suite that keeps up with you: it runs only the tests your changes actually affect, gives your AI coding agents a way to *prove* their work, and helps you test a new class of software — the AI agents and LLM-powered features now shipping in your applications.

Built on top of **PHP 8.4** and **PHPUnit 13**, this release brings together a set of features and first-party plugins that have been maturing quietly across the Pest 4 cycle — now stable, polished, and ready for prime time.

Below, we'll cover all the juicy details about this release. And as usual, you can find the [upgrade guide](/docs/upgrade-guide) on our website.

- **[Test Impact Analysis](#test-impact-analysis)**: Re-run only the tests affected by your latest changes — a 15-second suite replays in under a second.
- **[The Agent Browser](#the-agent-browser)**: Give your AI coding agents a single command to verify that a change actually works.
- **[Evals](#evals)**: Evaluate the quality of LLM agents and AI-generated output directly from your test suite.
- **[First-Party PHPStan Plugin](#first-party-phpstan-plugin)**: Teach PHPStan about Pest for accurate type inference and Pest-specific rules.
- **[Automated Refactoring With Rector](#automated-refactoring-with-rector)**: Modernize your test code and upgrade between Pest versions automatically.
- **[Time-Balanced Sharding](#time-balanced-sharding)**: Split your suite across CI jobs by real execution time, not naive test count.
- **[On Top of PHP 8.4 & PHPUnit 13](#on-top-of-php-84--phpunit-13)**: The latest platform, under the hood.

<a name="test-impact-analysis"></a>
## Test Impact Analysis

This is the one we've been most excited to share. **Test Impact Analysis (TIA)** drastically reduces the time it takes to run your test suite by re-running only the tests affected by your latest changes.

The first time you run with `--tia`, Pest records a graph of which tests depend on which files. Every run after that, Pest looks at what you changed, runs only the tests that touched those files, and replays cached results for everything else.

```bash
./vendor/bin/pest --parallel --tia
```

A typical Laravel suite that takes 15 seconds replays in under a second:

```
Tests:    774 passed (2658 assertions, 7 affected, 2 uncached, 765 replayed)
Duration: 0.74s
```

Edits to a single Blade template re-run a handful of feature tests. Comment-only edits, formatter passes, and README touches re-run nothing at all — Pest normalises file content before comparing, so cosmetic changes never enter the changed set.

Pest ships with sensible defaults for the most common PHP stacks (Laravel, Symfony, Livewire, Inertia, and browser assets), detecting each framework via Composer automatically. And for teams, CI can record the baseline once per merge to `main` so every developer downloads the result and starts replaying immediately — paying no record cost.

To learn more, check out the [Test Impact Analysis documentation](/docs/tia).

<a name="the-agent-browser"></a>
## The Agent Browser

AI coding agents are great at writing code, but they are terrible at knowing whether that code actually *works*. After editing a Blade template, a Livewire component, a controller, or a bit of CSS, an agent has no way to see the result — so it guesses, and moves on.

The **Agent Browser** plugin closes that loop. It gives your agent a single command to run a one-off verification against your application:

```bash
vendor/bin/pest --agent-browser="visit('/')->assertSee('Welcome');"
```

The agent gets a definitive pass or fail instead of a hopeful guess — with the full power of Pest at its disposal. Unlike browser-only agent tools that can only see the page, the Agent Browser runs *inside your real test suite*. Your agent can drive the UI **and** assert the side effects it triggered — submit a contact form, then assert the mail was sent — all in a single probe, with your factories, `RefreshDatabase`, and Laravel fakes available exactly as in a real feature test.

```bash
composer require pestphp/pest-plugin-agent-browser --dev
```

To learn more, check out the [Agent Browser documentation](/docs/agent-browser).

<a name="evals"></a>
## Evals

Testing software that talks to a Large Language Model is different from testing ordinary code. The same prompt can produce a different response every time, so a plain equality assertion is rarely enough. An *evaluation* — or "eval" — measures the *quality* of an AI's output rather than checking it against a single fixed value.

Pest's **Evals** plugin lets you write these evaluations with the same expressive `expect()` API you already use for your tests, combining deterministic checks with AI-powered scorers such as LLM-as-judge, semantic similarity, and agent trajectory analysis:

```php
use App\Agents\CapitalCityAgent;

it('answers capital city questions correctly', function (): void {
    expect(CapitalCityAgent::class)
        ->prompt('What is the capital of France?')
        ->toContain('Paris')          // deterministic check
        ->toBeRelevant()              // LLM-as-judge scorer
        ->toBeSimilar('Paris, France'); // semantic similarity
});
```

Because evals make real calls to an AI provider, they are excluded from your regular test run. To execute them, use the `--eval` option:

```bash
./vendor/bin/pest --eval
```

You can score safety, factual accuracy, and relevance; assert an agent called the right tools in the right order; sample the same prompt multiple times to prove consistency; and even write your own custom scorers. To learn more, check out the [Evals documentation](/docs/evals).

<a name="first-party-phpstan-plugin"></a>
## First-Party PHPStan Plugin

One of the most requested features from the community: **first-party PHPStan support**.

By default, PHPStan does not understand Pest's functional API — functions like `it()`, `test()`, `expect()`, and the `$this` available inside your test closures. Pest's PHPStan plugin teaches PHPStan about Pest, providing accurate type inference for your tests and expectations, and adding a set of Pest-specific rules that catch common mistakes before you run your suite:

```bash
composer require pestphp/pest-plugin-phpstan --dev
composer require phpstan/phpstan --dev
```

Now the type flowing through an `expect()` chain is fully understood, `$this` resolves to your configured test case, and PHPStan flags genuine mistakes in your tests — like an impossible expectation:

```php
expect(10)->toStartWith('1'); // int can never satisfy toStartWith()
```

On top of type inference, the plugin adds Pest-aware rules: static test closures, `$this` in `beforeAll()`, describe blocks without tests, duplicate test descriptions, empty test closures, invalid `throws()` and `covers()` references, and more. To learn more, check out the [PHPStan documentation](/docs/phpstan).

<a name="automated-refactoring-with-rector"></a>
## Automated Refactoring With Rector

Pest's **Rector** plugin provides automated refactoring rules powered by [Rector](https://getrector.org/). It helps simplify and modernize your test code — and upgrade between major Pest versions — automatically.

```bash
composer require pestphp/pest-plugin-rector --dev
composer require rector/rector --dev
```

With a set like `PEST_CODE_QUALITY`, dozens of rules convert raw PHP assertions into Pest's expressive matchers and chain redundant expectations together:

```diff
-expect(count($array))->toBe(5);
-expect(array_key_exists('id', $array))->toBeTrue();
+expect($array)->toHaveCount(5)
+    ->toHaveKey('id');
```

There are sets for PHPUnit-to-Pest migration, Laravel string matchers, Browser Testing assertions, and version upgrades — over 70 rules in total. To learn more, check out the [Rector documentation](/docs/rector).

<a name="time-balanced-sharding"></a>
## Time-Balanced Sharding

Pest 4 introduced test sharding — splitting your suite into chunks that run in parallel across multiple CI machines. Pest refines it with **time-balanced sharding**: instead of splitting tests evenly by count (which can leave one shard running much longer than the others), Pest distributes tests based on their *actual execution time*, so every shard finishes at roughly the same moment.

Generate the timing data once:

```bash
./vendor/bin/pest --update-shards
```

Then commit `tests/.pest/shards.json` to your repository. When `--shard` is used and this file exists, Pest automatically balances by time:

```bash
./vendor/bin/pest --shard=1/4
```

If you add new test files before updating the timings, your tests still run — new files are distributed evenly while known files remain time-balanced, and Pest reminds you to refresh the data. To learn more, check out [Optimizing Tests](/docs/optimizing-tests#test-sharding) and [Continuous Integration](/docs/continuous-integration#sharding-your-tests).

<a name="on-top-of-php-84--phpunit-13"></a>
## On Top of PHP 8.4 & PHPUnit 13

Pest 5 requires **PHP 8.4** and is built on top of **PHPUnit 13**, so you get all the latest language features and improvements from both. Be sure to check out the [PHPUnit 13 changelog](https://github.com/sebastianbergmann/phpunit/blob/13.0.0/ChangeLog-13.0.md) for the full details.

As always, we take breaking changes seriously. Upgrading from Pest 4 to Pest 5 is designed to be as effortless as previous upgrades — for most suites it's a one-line change in `composer.json`. See the [upgrade guide](/docs/upgrade-guide) for the details.

### Thanks To You, Pest 5 Is Here!

There's never been a better time to dive into testing and start using Pest. If you're ready to get started with Pest 5 right away, check out our [installation guide](/docs/installation) for step-by-step instructions. And if you're currently using an earlier version of Pest, we've got you covered with detailed upgrade instructions in our [upgrade guide](/docs/upgrade-guide).

Thank you for your continued support and feedback. We can't wait to see what you build with Pest 5!

---

Thank you for reading about Pest 5's new features! Want to get started with Pest? You can find the installation guide in the next section of the documentation: [Installation →](/docs/installation)
