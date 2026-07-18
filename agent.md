---
title: Agent
description: The Agent plugin gives AI coding agents a single command to verify that a change actually works — running inside your full test suite, and, with the Browser plugin installed, driving a real browser too.
---

# Agent

**Source code**: [github.com/pestphp/pest-plugin-agent](https://github.com/pestphp/pest-plugin-agent)

AI coding agents excel at writing code, yet they often have no way to know whether that code actually works. After editing a controller, a Livewire component, a Blade template, or a bit of CSS, an agent cannot *see* the result — so it guesses, then moves on.

Thankfully, the Agent plugin closes that loop. It gives your agent a single command to run a one-off verification against your application:

```bash
./vendor/bin/pest --agent='$user = \App\Models\User::factory()->create(); $this->actingAs($user)->get("/dashboard")->assertOk();'
```

Your agent receives a definitive pass or fail instead of a hopeful guess, with the full power of Pest at its disposal. Everything your test suite can do is available: factories, the database, mail and notification fakes, authentication, and expectations — all in the same probe.

> **Note:** The snippet is wrapped in single quotes. Single quotes tell the shell to pass everything through to PHP untouched, so `$variables` and `\App` class names need no escaping — with double outer quotes, your shell would interpolate `$user` to an empty string before PHP ever sees it. Within the snippet, you may use double quotes for PHP string literals.

The plugin is not specific to the browser. It verifies *any* code your test suite can reach — backend behavior, queued jobs, mail, notifications, and more — with nothing beyond a standard Pest install. That said, it truly shines once the [Browser Testing](/docs/browser-testing) plugin is installed, because your agent may then drive a real browser *and* assert the side effects it triggers, all in a single command.

## Getting Started

To get started with the Agent plugin, require it via Composer:

```bash
composer require pestphp/pest-plugin-agent --dev
```

The plugin adds the `--agent` option to Pest. This alone is enough to verify backend behavior. However, to also verify frontend behavior — visiting pages, screenshots, clicks, responsive checks — you should install the [Browser Testing](/docs/browser-testing) plugin as well. It is optional, yet highly recommended, as it is where the Agent plugin truly shines:

```bash
composer require pestphp/pest-plugin-browser --dev

npm install playwright@latest
npx playwright install
```

Finally, teach your AI agent how to use the plugin by installing its guidelines and skills through [Laravel Boost](https://github.com/laravel/boost):

```bash
php artisan boost:install
```

When prompted for third-party AI guidelines and skills, select `pestphp/pest-plugin-agent`. This installs the guidelines and the `pest-plugin-agent` skill, so your agent knows exactly when and how to reach for the `--agent` command.

That's it. Your agent may now verify backend behavior, frontend behavior, or both, from a single command.

## How It Works

When you run `./vendor/bin/pest --agent='<code>'`, Pest writes your snippet into a temporary test file that resembles the following:

```php
<?php

it('verify', function () {
    // your snippet goes here...
});
```

The file runs with your project's real Pest configuration: the classes and traits you registered in `tests/Pest.php` via `uses()` — including `RefreshDatabase` — are applied to the generated test automatically, so the snippet behaves exactly like a test living in your test suite. Once the run finishes, the temporary file is removed.

There are a few details worth keeping in mind:

- **Every class must be fully qualified.** The generated file contains no `use` imports, so your snippet should reference `\App\Models\User` rather than `User`.
- **Snippets may not be empty.** Passing `--agent` without a value, or with an empty one, will abort the run with an error instead of silently passing.
- **Directory-scoped hooks do not apply.** Classes and traits from `uses(...)->in('Feature')` carry over to the generated test; however, `beforeEach()` hooks attached to a directory are bound to that path and will not run for the snippet. If required setup lives in such a hook, your agent should inline it at the top of the snippet.

## Why Agent?

A new category of tooling has emerged to give agents "eyes" on the browser — Vercel's [agent-browser](https://github.com/vercel-labs/agent-browser) being a prominent example. These tools drive a headless Chromium instance and let an agent click, type, and screenshot its way through your app.

They are helpful; however, they share a fundamental limitation: they only see the browser. An agent driving a raw browser-automation CLI can confirm that a page rendered, but it cannot confirm that the email was queued, the order was written to the database, the notification fired, or the job was dispatched. It is testing your application from the outside, blind to everything that happens behind the response.

The Agent plugin is different because it is backed by your actual test suite, and — with the Browser plugin installed — by Pest's real browser testing engine as well. This gives it two advantages no browser-only tool can match:

- **Full-stack verification in a single probe.** Your agent can drive the UI *and* assert the side effects it triggers — in the same command. Submit a contact form in the browser, then assert the mail was sent. Register a user, then assert the welcome notification fired. This is impossible when the browser and the backend are two disconnected worlds.
- **The same truth your tests assert against.** Snippets run with your project's real Pest configuration — the traits from `tests/Pest.php`, `RefreshDatabase`, your factories, seeders, and helpers. What the agent verifies maps one-to-one onto the regression test you'd write by hand. A browser-automation CLI, by contrast, has no idea your `User` factory or `Order` model even exist.

In short: browser-only agent tools verify what the *page* looks like. The Agent plugin verifies what your *application* actually did — because your real test suite is running behind the scenes.

## Verifying Backend Behavior

The snippet runs inside a full Pest test, so your agent may seed state with factories and assert against it directly. Typically, you should create state inline rather than relying on pre-existing data:

```bash
./vendor/bin/pest --agent='$post = \App\Models\Post::factory()->create(); expect($post->author)->not->toBeNull();'
```

As you would expect, acting as an authenticated user and asserting a response works exactly as it would in a real feature test:

```bash
./vendor/bin/pest --agent='$user = \App\Models\User::factory()->create(); $this->actingAs($user)->get("/dashboard")->assertOk();'
```

Mail, notifications, and queued jobs are all verifiable through Laravel's standard fakes:

```bash
./vendor/bin/pest --agent='\Illuminate\Support\Facades\Notification::fake(); \App\Models\User::factory()->create()->notify(new \App\Notifications\Welcome()); \Illuminate\Support\Facades\Notification::assertSentTo(\App\Models\User::first(), \App\Notifications\Welcome::class);'
```

None of this requires the browser — a standard Pest install is all you need to verify behavior end to end on the backend.

## Verifying Frontend Behavior

With the Browser Testing plugin installed, your agent may visit pages, take screenshots, assert content, and interact with the UI — all driven by a real browser:

```bash
# Take a screenshot to visually confirm a change
./vendor/bin/pest --agent='visit("/")->screenshot(filename: "homepage");'

# Assert visible content
./vendor/bin/pest --agent='visit("/")->assertSee("Welcome");'

# Drive an interaction flow
./vendor/bin/pest --agent='visit("/")->click("Login")->assertPathIs("/login");'
```

Your agent may also check responsive layouts by emulating devices or setting an explicit viewport:

```bash
./vendor/bin/pest --agent='visit("/")->on()->mobile()->screenshot(filename: "home-mobile");'
./vendor/bin/pest --agent='visit("/")->on()->iPhone14Pro()->screenshot(filename: "home-iphone");'
```

In addition, it may run health checks for JavaScript errors, accessibility issues, and visual drift:

```bash
./vendor/bin/pest --agent='visit("/")->assertNoJavaScriptErrors();'
./vendor/bin/pest --agent='visit("/")->assertNoAccessibilityIssues();'
```

For the complete browser API, see the [Browser Testing](/docs/browser-testing) documentation.

## Combining Frontend and Backend

This is where the Agent plugin truly shines, and the reason the [Browser Testing](/docs/browser-testing) plugin is so highly recommended. Because the browser and your application live in the same probe, your agent may drive the UI and then assert the side effect it produced — the exact end-to-end confidence a browser-only tool can never provide:

```bash
./vendor/bin/pest --agent='\Illuminate\Support\Facades\Mail::fake(); visit("/contact")->type("email", "test@example.com")->type("message", "Hello")->press("Send")->assertSee("Message sent"); \Illuminate\Support\Facades\Mail::assertSent(\App\Mail\ContactForm::class);'
```

Or, you may drive a checkout flow in the browser and then assert directly against the database:

```bash
./vendor/bin/pest --agent='visit("/checkout")->type("card", "4242424242424242")->press("Pay")->assertSee("Transaction processed"); expect(\App\Models\Order::count())->toBe(1);'
```

Typically, you should assert a frontend signal first — such as `assertSee` or `assertPathIs` — so that you know the action was processed before checking what it touched on the backend.

## Running Multiple Verifications

Sometimes you may wish to verify more than one behavior in a single run. To accomplish this, you may pass the `--agent` option multiple times — each snippet becomes its own isolated test:

```bash
./vendor/bin/pest --agent='expect(\App\Models\Order::count())->toBe(1);' --agent='visit("/")->assertSee("Welcome");'
```

Every snippet reports its result under the test name `verify`, so you should keep each snippet focused on a single behavior — batching unrelated checks into one snippet makes it harder to tell which one broke.

## When to Use It

The Agent plugin is a verification probe, not a replacement for your test suite. Reach for it when:

- An agent has just made a change and needs to confirm it works — a route still returns `200`, a page still renders, a relationship resolves.
- You want to visually review a Blade, Livewire, CSS, or JavaScript change with a quick screenshot.
- You need a one-off behavioral check that doesn't warrant a permanent test file.

When the behavior deserves a lasting regression guard, write a real test in `tests/Feature` or `tests/Browser` instead. The Agent plugin is designed to give agents fast, honest feedback while they work — not to skip the tests that keep your application healthy.

---

Next, let's dive into architectural testing and how it can help you evaluate the overall design of your application and catch potential flaws before they become significant issues: [Architecture Testing](/docs/arch-testing)
