---
title: Pest 5 Now Available
description: Today, we're thrilled to announce the release of Pest 5. Built on PHP 8.4 and PHPUnit 13, Pest 5 introduces the Tia Engine, the Agent Browser, AI Evals, a first-party PHPStan plugin, automated refactoring with Rector, time-balanced sharding, and much more.
---

# Pest 5 Now Available

After shipping Pest 4, with the best browser testing in the world, we thought a release as big as Pest 4 wouldn't be possible again.

And yet, today I'm proud to introduce you to the biggest release of Pest yet: **Pest 5**.

Below, we'll cover all the details of this release. As usual, you may find the [upgrade guide](/docs/upgrade-guide) on our website.

- **[Tia Engine](#test-impact-analysis)**: The engine that re-runs only the tests affected by your latest changes, powered by the smartest dependency tree ever seen — editing a `button.tsx`, for example, re-runs only the tests rendering the Inertia pages that use it. A test suite that used to take 10 minutes now replays in around 4 seconds, and because each replay restores the exact paths its test covered, code coverage reports the same numbers as a full run.
- **[The Agent Browser](#the-agent-browser)**: Give your AI coding agents a single command to verify that a change actually works. Unlike Vercel's agent browser, a single bash/tool call creates users, logs them in, runs the full navigation and clicks, and makes assertions — including backend assertions that check things like whether emails actually got sent.
- **[Evals](#evals)**: Evaluate the quality of LLM agents and AI-generated output directly from your test suite, combining deterministic checks with AI-powered scorers — LLM-as-judge, semantic similarity, safety, and tool-trajectory analysis — all through the same `expect()` API.
- **[First-Party PHPStan Plugin](#first-party-phpstan-plugin)**: Teach PHPStan about Pest's functional API — `it()`, `expect()`, `$this` — so your tests are as fully typed as your app, catching impossible expectations and dozens of Pest-specific mistakes before you even run the suite.
- **[Automated Refactoring With Rector](#automated-refactoring-with-rector)**: Over 70 rules that modernize your test code, convert raw PHP and PHPUnit assertions into Pest's expressive matchers, and upgrade you between major Pest versions — automatically.
- **[Time-Balanced Sharding](#time-balanced-sharding)**: Sharding splits your suite across several CI machines that run in parallel, so a large suite finishes in a fraction of the time. Until now those shards were divided by test count, which left one machine grinding through the slow tests while the others finished early and sat idle. Pest 5 divides them by real execution time instead, so every shard carries an equal share of the work and they all finish at the same moment.

Built on top of **PHP 8.4** and **PHPUnit 13**, this release brings together a set of features and first-party plugins that have been maturing quietly across the Pest 4 cycle — now stable, polished, and ready for prime time.

<a name="test-impact-analysis"></a>
## Tia Engine

This is the one we've been most excited to share. The **Tia Engine** — short for Test Impact Analysis — drastically reduces the time it takes to run your test suite by re-running only the tests affected by your latest changes.

The first time you run with `--tia`, the engine records a graph of which tests depend on which files. Every run after that, the engine looks at what you changed, runs only the tests that touched those files, and replays cached results for everything else:

```bash
./vendor/bin/pest --parallel --tia
```

A typical Laravel suite that used to take 10 minutes now replays in around 4 seconds:

```
Tests:    774 passed (2658 assertions, 7 affected, 2 uncached, 765 replayed)
Duration: 3.92s
```

A replay isn't a shortcut that skips work — it's a faithful reconstruction of the real run. When the engine caches a test, it stores not just the pass or fail result but everything that test produced, including the exact lines and branches it covered. So a replayed run reports the same code coverage as a full run, and everything that depends on it keeps working — coverage thresholds, the `--coverage` report, and `--min` all behave exactly as if every test had executed from scratch. You get the speed of replaying with none of the fidelity lost.

What makes this possible is the smartest dependency tree we've ever built. The engine doesn't just map files to tests — it understands your whole stack. A column rename in a migration re-runs only the tests that queried that table. A change to an Inertia page re-runs only the tests that server-side-rendered it, and editing a shared JS component walks Vite's module graph to find every page that imports it. Blade templates re-run the tests that rendered them, browser assets re-run only browser tests, and arch tests re-run whenever your source's shape changes. Edit a single Blade template and a handful of feature tests run; touch `config/app.php` and Pest re-runs everything, because it can't statically prove otherwise.

Comment-only edits, formatter passes, and README touches re-run nothing at all — the engine normalizes file content before comparing (stripping whitespace, comments, and docblocks), so a Pint pass or a Prettier reformat produces an identical hash and never enters the changed set.

The engine ships with sensible defaults for the most common PHP stacks (Laravel, Symfony, Livewire, Inertia, and browser assets), detecting each framework via Composer automatically. And for teams, CI can record the baseline once per merge to `main` so every developer downloads the result and starts replaying immediately — paying no record cost.

To learn more, check out the [Tia Engine documentation](/docs/tia).

<a name="the-agent-browser"></a>
## The Agent Browser

AI coding agents are great at writing code, but they are terrible at knowing whether that code actually *works*. After editing a Blade template, a Livewire component, a controller, or a bit of CSS, an agent has no way to see the result — so it guesses, and moves on.

The **Agent Browser** plugin closes that loop. It gives your agent a single command to run a one-off verification against your application:

```bash
vendor/bin/pest --agent-browser="visit('/')->assertSee('Welcome');"
```

The agent gets a definitive pass or fail instead of a hopeful guess — with the full power of Pest at its disposal. This is where the Agent Browser pulls ahead of browser-only agent tools like Vercel's agent browser: those tools live outside your application and can only observe what the page renders, so they confirm the UI *looks* right but never that the system *behaved* right. A green screenshot from a tool like Vercel's tells you nothing about whether the job was queued, the mail was sent, or the row was written.

The Agent Browser runs *inside your real test suite* instead. Your agent can drive the UI **and** assert the side effects it triggered — submit a contact form, then assert the mail was sent — all in a single probe, with your factories, `RefreshDatabase`, and Laravel fakes available exactly as in a real feature test. Where Vercel's agent browser is a black box bolted onto the outside of your app, the Agent Browser has the same full-stack visibility your own feature tests do, so a passing check means the whole flow — front to back — actually works.

To get started, install the plugin via Composer:

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

There's far more you can score: assert an agent resists prompt injection and stays on topic with `toBeSafe()`, check factual accuracy against a reference answer, verify an agent called the right tools in the right order with `toFollowTrajectory()`, sample the same prompt multiple times with `repeat()` to prove consistency, and even write your own custom scorers. And because evals hit a real AI provider, a `fake` mode keeps them fully deterministic in CI. To learn more, check out the [Evals documentation](/docs/evals).

<a name="first-party-phpstan-plugin"></a>
## First-Party PHPStan Plugin

One of the most requested features from the community: **first-party PHPStan support**.

By default, PHPStan does not understand Pest's functional API — functions like `it()`, `test()`, `expect()`, and the `$this` available inside your test closures. Pest's PHPStan plugin teaches PHPStan about Pest, providing accurate type inference for your tests and expectations, and adding a set of Pest-specific rules that catch common mistakes before you run your suite:

```bash
composer require pestphp/pest-plugin-phpstan --dev
composer require phpstan/phpstan --dev
```

Now the type flowing through an `expect()` chain is fully understood — including higher-order expectations like `expect($user)->name->toBe('Nuno')` and any methods you share through `pest()->extend()` or `uses()`. `$this` resolves to your configured test case, and PHPStan flags genuine mistakes in your tests — like an impossible expectation:

```php
expect(10)->toStartWith('1'); // int can never satisfy toStartWith()
```

On top of type inference, the plugin adds Pest-aware rules: static test closures, `$this` in `beforeAll()`, describe blocks without tests, duplicate test descriptions, empty test closures, invalid `throws()` and `covers()` references, and more. To learn more, check out the [PHPStan documentation](/docs/phpstan).

<a name="automated-refactoring-with-rector"></a>
## Automated Refactoring With Rector

Pest's **Rector** plugin provides automated refactoring rules powered by [Rector](https://getrector.org/). It helps simplify and modernize your test code — and upgrade between major Pest versions — automatically.

To get started, install the plugin via Composer along with Rector:

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

<a name="new-expectations"></a>
## New Expectations

Pest 5 also brings a couple of new additions to the expectation API. Sometimes you may wish to assert that a value is a well-formed email address or a valid ULID — two checks common enough that writing them by hand quickly becomes tedious.

Thankfully, Pest now provides `toBeEmail()` and `toBeUlid()` for exactly these cases:

```php
expect('nuno@pestphp.com')->toBeEmail();
expect('01ARZ3NDEKTSV4RRFFQ69G5FAV')->toBeUlid();
```

Of course, both expectations may be negated with `not`, and you may pass a custom failure message as the first argument. To explore the full set of available matchers, check out the [Expectations documentation](/docs/expectations).

<a name="on-top-of-php-84--phpunit-13"></a>
## On Top of PHP 8.4 & PHPUnit 13

Pest 5 requires **PHP 8.4** and is built on top of **PHPUnit 13**, so you get all the latest language features and improvements from both. Be sure to check out the [PHPUnit 13 changelog](https://github.com/sebastianbergmann/phpunit/blob/13.0.0/ChangeLog-13.0.md) for the full details.

As always, we take breaking changes seriously. Upgrading from Pest 4 to Pest 5 is designed to be as effortless as previous upgrades — for most suites it's a one-line change in `composer.json`. See the [upgrade guide](/docs/upgrade-guide) for the details.

### Thanks To You, Pest 5 Is Here!

There's never been a better time to dive into testing and start using Pest. If you're ready to get started with Pest 5 right away, check out our [installation guide](/docs/installation) for step-by-step instructions. And if you're currently using an earlier version of Pest, we've got you covered with detailed upgrade instructions in our [upgrade guide](/docs/upgrade-guide).

Thank you for your continued support and feedback. We can't wait to see what you build with Pest 5!

---

Thank you for reading about Pest 5's new features! Want to get started with Pest? You can find the installation guide in the next section of the documentation: [Installation →](/docs/installation)
