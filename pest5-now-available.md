---
title: Pest 5 Now Available
description: Today, we're thrilled to announce the release of Pest 5 — built on PHP 8.4 and PHPUnit 13, introducing the Tia Engine, the Agent plugin, AI Evals, a first-party PHPStan plugin, automated refactoring with Rector, time-balanced sharding, and much more.
---

# Pest 5 Now Available

Today, we're thrilled to announce the release of the biggest version of Pest yet: **Pest 5**. Built on top of **PHP 8.4** and **PHPUnit 13**, this release brings together a set of features and first-party plugins that have been maturing quietly across the Pest 4 cycle — now stable, polished, and ready for prime time:

- **[Tia Engine](#test-impact-analysis)**: Re-run only the tests affected by your latest changes — a suite that used to take 10 minutes now replays in around 4 seconds, with no loss in code coverage fidelity.
- **[The Agent Plugin](#the-agent)**: Give your AI coding agents a single command to verify that a change actually works — running inside your real test suite, and, with the Browser plugin installed, driving a real browser too.
- **[Evals](#evals)**: Evaluate the quality of LLM agents and AI-generated output directly from your test suite, combining deterministic checks with AI-powered scorers through the same `expect()` API.
- **[First-Party PHPStan Plugin](#first-party-phpstan-plugin)**: Teach PHPStan about Pest's functional API — `it()`, `expect()`, `$this` — so your tests are as fully typed as your app.
- **[Automated Refactoring With Rector](#automated-refactoring-with-rector)**: Dozens of rules that modernize your test code and convert raw PHP assertions into Pest's expressive matchers — automatically.
- **[Time-Balanced Sharding](#time-balanced-sharding)**: Split your suite across CI machines by real execution time instead of test count, so every shard finishes at the same moment.

Below, we'll cover how to get started with each of these features. For the full details, each section links to its dedicated documentation page.

<a name="upgrading-to-pest-5"></a>
## Upgrading To Pest 5

For most suites, upgrading from Pest 4 is a one-line change in your application's `composer.json` file:

```diff
-    "pestphp/pest": "^4.0",
+    "pestphp/pest": "^5.0",
```

All other Pest maintained plugins should be updated to version `^5.0` as well. Note that Pest 5 requires **PHP 8.4** or greater. For the complete list of changes, check out the [upgrade guide](/docs/upgrade-guide).

<a name="test-impact-analysis"></a>
## Tia Engine

This is the one we've been most excited to share. The **Tia Engine** — short for Test Impact Analysis — drastically reduces the time it takes to run your test suite by re-running only the tests affected by your latest changes.

To get started, add the `--tia` flag to any Pest invocation:

```bash
./vendor/bin/pest --parallel --tia
```

The first run records a graph of which tests depend on which files — this requires a code coverage driver such as [PCOV](https://github.com/krakjoe/pcov) or [Xdebug](https://xdebug.org/). Every run after that, the engine looks at what you changed, runs only the tests that touched those files, and replays cached results for everything else. A typical Laravel suite that used to take 10 minutes now replays in around 4 seconds:

```
Tests:    774 passed (2658 assertions, 7 affected, 2 uncached, 765 replayed)
Duration: 3.92s
```

A replay isn't a shortcut that skips work — each cached test stores everything it produced, including the exact lines and branches it covered, so a replayed run reports the same code coverage as a full run. And the dependency tree understands your whole stack: a migration change re-runs only the tests that queried that table, editing a shared JS component walks Vite's module graph to find every Inertia page that imports it, and a comment-only edit or formatter pass re-runs nothing at all. Pest detects Laravel, Symfony, Livewire, Inertia, and browser assets automatically via Composer.

For teams, you may have CI record the baseline once per merge to `main` so every developer downloads the result and starts replaying immediately. To learn more, check out the [Tia Engine documentation](/docs/tia).

<a name="the-agent"></a>
## The Agent Plugin

AI coding agents are great at writing code, but they are terrible at knowing whether that code actually *works*. The **Agent** plugin closes that loop, giving your agent a single command to run a one-off verification against your application.

To get started, install the plugin via Composer:

```bash
composer require pestphp/pest-plugin-agent --dev
```

The plugin adds the `--agent` option to Pest, which runs a snippet inside a full Pest test — with your factories, `RefreshDatabase`, and Laravel fakes available exactly as in a real feature test:

```bash
./vendor/bin/pest --agent='$user = \App\Models\User::factory()->create(); $this->actingAs($user)->get("/dashboard")->assertOk();'
```

With the [Browser Testing](/docs/browser-testing) plugin installed, your agent may also drive a real browser **and** assert the side effects it triggered — submit a contact form, then assert the mail was sent — all in a single probe:

```bash
./vendor/bin/pest --agent='visit("/")->assertSee("Welcome");'
```

This is where the Agent plugin pulls ahead of browser-only agent tools like Vercel's agent browser: those tools can confirm the UI *looks* right, but never that the job was queued, the mail was sent, or the row was written. The Agent plugin runs inside your real test suite, so a passing check means the whole flow — front to back — actually works.

To learn more, including how to teach your agent to use the plugin via [Laravel Boost](https://github.com/laravel/boost), check out the [Agent documentation](/docs/agent).

<a name="evals"></a>
## Evals

Testing software that talks to a Large Language Model is different from testing ordinary code. The same prompt can produce a different response every time, so a plain equality assertion is rarely enough. Pest's **Evals** plugin lets you evaluate the *quality* of an AI's output with the same expressive `expect()` API you already use for your tests.

To get started, install the plugin via Composer:

```bash
composer require pestphp/pest-plugin-evals --dev
```

Then, write an eval — combining deterministic checks with AI-powered scorers such as LLM-as-judge and semantic similarity:

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

Because each eval calls a real model, evals are skipped on a regular test run — your suite stays fast and free, with no API calls by default. Add the `--evals` option to run them for real:

```bash
./vendor/bin/pest            # evals skipped, no API calls
./vendor/bin/pest --evals    # real model, all scorers active
```

There's far more you can score: assert an agent resists prompt injection with `toBeSafe()`, check factual accuracy with `toBeFactual()`, verify an agent called the right tools in the right order with `toFollowTrajectory()`, sample the same prompt multiple times with `repeat()`, and even write your own custom scorers. To learn more, check out the [Evals documentation](/docs/evals).

<a name="first-party-phpstan-plugin"></a>
## First-Party PHPStan Plugin

One of the most requested features from the community: **first-party PHPStan support**. By default, PHPStan does not understand Pest's functional API — functions like `it()`, `test()`, `expect()`, and the `$this` available inside your test closures. Pest's PHPStan plugin teaches PHPStan about Pest.

To get started, install the plugin via Composer along with PHPStan:

```bash
composer require pestphp/pest-plugin-phpstan --dev
composer require phpstan/phpstan --dev
```

If you use `phpstan/extension-installer`, the plugin is registered automatically. Otherwise, include the extension in your `phpstan.neon` configuration file:

```neon
includes:
    - vendor/pestphp/pest-plugin-phpstan/extension.neon
```

Now the type flowing through an `expect()` chain is fully understood — including higher-order expectations like `expect($user)->name->toBe('Nuno')` — and PHPStan flags genuine mistakes in your tests, like an impossible expectation:

```php
expect(10)->toStartWith('1'); // int can never satisfy toStartWith()
```

On top of type inference, the plugin adds Pest-aware rules: static test closures, `$this` in `beforeAll()`, duplicate test descriptions, invalid `throws()` and `covers()` references, and more. To learn more, check out the [PHPStan documentation](/docs/phpstan).

<a name="automated-refactoring-with-rector"></a>
## Automated Refactoring With Rector

Pest's **Rector** plugin provides automated refactoring rules powered by [Rector](https://getrector.org/). It helps simplify and modernize your test code — and upgrade between major Pest versions — automatically.

To get started, install the plugin via Composer along with Rector:

```bash
composer require pestphp/pest-plugin-rector --dev
composer require rector/rector --dev
```

Then, add one of the predefined rule sets to your `rector.php` file:

```php
use Pest\Rector\Set\PestSetList;
use Rector\Config\RectorConfig;

return RectorConfig::configure()
    ->withPaths([__DIR__ . '/tests'])
    ->withSets([
        PestSetList::CODING_STYLE,
    ]);
```

With this set, dozens of rules convert raw PHP assertions into Pest's expressive matchers and chain redundant expectations together:

```diff
-expect(count($array))->toBe(5);
-expect(array_key_exists('id', $array))->toBeTrue();
+expect($array)->toHaveCount(5)
+    ->toHaveKey('id');
```

You may preview the changes with `vendor/bin/rector process --dry-run` before applying them. There are sets for coding style and version upgrades — 60 rules in total. To learn more, check out the [Rector documentation](/docs/rector).

<a name="time-balanced-sharding"></a>
## Time-Balanced Sharding

Pest 4 introduced test sharding — splitting your suite into chunks that run in parallel across multiple CI machines. Pest 5 refines it with **time-balanced sharding**: instead of splitting tests evenly by count (which can leave one shard running much longer than the others), Pest distributes tests based on their *actual execution time*, so every shard finishes at roughly the same moment.

To get started, generate the timing data once:

```bash
./vendor/bin/pest --update-shards
```

Then, commit `tests/.pest/shards.json` to your repository. When `--shard` is used and this file exists, Pest automatically balances by time:

```bash
./vendor/bin/pest --shard=1/4
```

If you add new test files before updating the timings, your tests still run — new files are distributed evenly while known files remain time-balanced, and Pest reminds you to refresh the data. To learn more, check out [Optimizing Tests](/docs/optimizing-tests#test-sharding) and [Continuous Integration](/docs/continuous-integration#sharding-your-tests).

<a name="new-expectations"></a>
## New Expectations

Pest 5 also brings new additions to the expectation API. Sometimes you may wish to assert that a value is a well-formed email address, a valid IP address, or a ULID — checks common enough that writing them by hand quickly becomes tedious.

Thankfully, Pest now provides `toBeEmail()`, `toBeUlid()`, `toBeIpAddress()`, `toBeMacAddress()`, `toBeHostname()`, `toBeDomain()`, `toBeBase64()`, and `toBeHexadecimal()` for exactly these cases:

```php
expect('nuno@pestphp.com')->toBeEmail();
expect('01ARZ3NDEKTSV4RRFFQ69G5FAV')->toBeUlid();
expect('192.168.1.1')->toBeIpAddress();
expect('00:1a:2b:3c:4d:5e')->toBeMacAddress();
expect('example.com')->toBeHostname();
expect('example.co.uk')->toBeDomain();
expect('Zm9vYmFy')->toBeBase64();
expect('deadbeef')->toBeHexadecimal();
```

Of course, each of these expectations may be negated with `not`. To explore the full set of available matchers, check out the [Expectations documentation](/docs/expectations).

<a name="on-top-of-php-84--phpunit-13"></a>
## On Top of PHP 8.4 & PHPUnit 13

Pest 5 requires **PHP 8.4** and is built on top of **PHPUnit 13**, so you get all the latest language features and improvements from both. Be sure to check out the [PHPUnit 13 changelog](https://github.com/sebastianbergmann/phpunit/blob/13.0.0/ChangeLog-13.0.md) for the full details.

### Thanks To You, Pest 5 Is Here!

There's never been a better time to dive into testing and start using Pest. If you're ready to get started with Pest 5 right away, check out our [installation guide](/docs/installation), and if you're currently using an earlier version of Pest, we've got you covered with our [upgrade guide](/docs/upgrade-guide).

Thank you for your continued support and feedback. We can't wait to see what you build with Pest 5!

---

Thank you for reading about Pest 5's new features! Want to get started with Pest? You can find the installation guide in the next section of the documentation: [Installation →](/docs/installation)
