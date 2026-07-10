---
title: Agent Browser
description: The Agent Browser plugin gives AI coding agents a single command to verify that a change actually works — driving a real browser and your full test suite behind the scenes, without ever writing a throwaway test file.
---

# Agent Browser

**Source code**: [github.com/pestphp/pest-plugin-agent-browser](https://github.com/pestphp/pest-plugin-agent-browser)

AI coding agents are great at writing code, but they are terrible at knowing whether that code actually works. After editing a Blade template, a Livewire component, a controller, or a bit of CSS, an agent has no way to *see* the result — so it guesses, and moves on.

The Agent Browser plugin closes that loop. It gives your agent a single command to run a one-off verification against your application:

```bash
vendor/bin/pest --agent-browser="visit('/')->assertSee('Welcome');"
```

The agent gets a definitive pass or fail instead of a hopeful guess — with the full power of Pest at its disposal. Everything your test suite can do is available: factories, the database, mail and notification fakes, authentication, expectations, *and* real browser testing — all in the same probe.

## Getting Started

To start using the Agent Browser plugin, require it via Composer.

```bash
composer require pestphp/pest-plugin-agent-browser --dev
```

The plugin adds the `--agent-browser` option to Pest. For browser-based verification (visiting pages, screenshots, clicks, responsive checks), you'll also want the [Browser Testing](/docs/browser-testing) plugin installed:

```bash
composer require pestphp/pest-plugin-browser --dev

npm install playwright@latest
npx playwright install
```

Finally, teach your AI agent how to use the plugin by installing its guidelines and skills through [Laravel Boost](https://github.com/laravel/boost):

```bash
php artisan boost:install
```

When prompted for third-party AI guidelines and skills, select `pestphp/pest-plugin-agent-browser`. This installs the guidelines and the `pest-plugin-agent-browser` skill, so your agent knows exactly when and how to reach for the `--agent-browser` command.

That's it. Your agent can now verify backend behavior, frontend behavior, or both, from a single command.

## Why Agent Browser?

A new category of tooling has emerged to give agents "eyes" on the browser — Vercel's [agent-browser](https://github.com/vercel-labs/agent-browser) being a prominent example. These tools drive a headless Chromium instance and let an agent click, type, and screenshot its way through your app.

They are genuinely useful, but they share a fundamental limitation: **they only see the browser.** An agent driving a raw browser-automation CLI can confirm that a page rendered, but it cannot confirm that the email was queued, the order was written to the database, the notification fired, or the job was dispatched. It is testing your application from the outside, blind to everything that happens behind the response.

The Agent Browser plugin is different because it is **backed by Pest's real browser testing engine and runs inside your actual test suite.** This gives it two advantages no browser-only tool can match:

- **Full-stack verification in a single probe.** Your agent can drive the UI *and* assert the side effects it triggers — in the same command. Submit a contact form in the browser, then assert the mail was sent. Register a user, then assert the welcome notification fired. This is impossible when the browser and the backend are two disconnected worlds.
- **The same truth your tests assert against.** Snippets run with your project's real Pest configuration — the traits from `tests/Pest.php`, `RefreshDatabase`, your factories, seeders, and helpers. What the agent verifies maps one-to-one onto the regression test you'd write by hand. A browser-automation CLI, by contrast, has no idea your `User` factory or `Order` model even exist.

In short: browser-only agent tools verify what the *page* looks like. The Agent Browser plugin verifies what your *application* actually did — because real browser testing is running behind the scenes.

## Verifying Backend Behavior

The snippet runs inside a full Pest test, so your agent can seed state with factories and assert against it directly. State should always be created inline — never rely on pre-existing data.

```bash
vendor/bin/pest --agent-browser="\$post = \App\Models\Post::factory()->create(); expect(\$post->author)->not->toBeNull();"
```

Acting as an authenticated user and asserting a response works exactly as it would in a real feature test:

```bash
vendor/bin/pest --agent-browser="\$user = \App\Models\User::factory()->create(); \$this->actingAs(\$user)->get('/dashboard')->assertOk();"
```

Mail, notifications, and queued jobs are all verifiable through Laravel's standard fakes:

```bash
vendor/bin/pest --agent-browser="\Illuminate\Support\Facades\Notification::fake(); \App\Models\User::factory()->create()->notify(new \App\Notifications\Welcome()); \Illuminate\Support\Facades\Notification::assertSentTo(\App\Models\User::first(), \App\Notifications\Welcome::class);"
```

## Verifying Frontend Behavior

With the Browser Testing plugin installed, your agent can visit pages, take screenshots, assert content, and interact with the UI — all driven by a real browser.

```bash
# Take a screenshot to visually confirm a change
vendor/bin/pest --agent-browser="visit('/')->screenshot(filename: 'homepage');"

# Assert visible content
vendor/bin/pest --agent-browser="visit('/')->assertSee('Welcome');"

# Drive an interaction flow
vendor/bin/pest --agent-browser="visit('/')->click('Login')->assertPathIs('/login');"
```

It can check responsive layouts by emulating devices or setting an explicit viewport:

```bash
vendor/bin/pest --agent-browser="visit('/')->on()->mobile()->screenshot(filename: 'home-mobile');"
vendor/bin/pest --agent-browser="visit('/')->on()->iPhone14Pro()->screenshot(filename: 'home-iphone');"
```

And it can run health checks for JavaScript errors, accessibility issues, and visual drift:

```bash
vendor/bin/pest --agent-browser="visit('/')->assertNoJavaScriptErrors();"
vendor/bin/pest --agent-browser="visit('/')->assertNoAccessibilityIssues();"
```

For the complete browser API, see the [Browser Testing](/docs/browser-testing) documentation.

## Combining Frontend and Backend

This is where the Agent Browser plugin truly shines. Because the browser and your application live in the same probe, your agent can drive the UI and then assert the side effect it produced — the exact end-to-end confidence a browser-only tool can never provide.

```bash
vendor/bin/pest --agent-browser="\Illuminate\Support\Facades\Mail::fake(); visit('/contact')->type('email', 'test@example.com')->type('message', 'Hello')->press('Send')->assertSee('Message sent'); \Illuminate\Support\Facades\Mail::assertSent(\App\Mail\ContactForm::class);"
```

```bash
vendor/bin/pest --agent-browser="visit('/checkout')->type('card', '4242424242424242')->press('Pay')->assertSee('Transaction processed'); expect(\App\Models\Order::count())->toBe(1);"
```

Always assert a frontend signal first (like `assertSee` or `assertPathIs`) so you know the action was processed before checking what it touched on the backend.

## When to Use It

The Agent Browser plugin is a **verification probe**, not a replacement for your test suite. Reach for it when:

- An agent has just made a change and needs to confirm it works — a route still returns `200`, a page still renders, a relationship resolves.
- You want to visually review a Blade, Livewire, CSS, or JavaScript change with a quick screenshot.
- You need a one-off behavioral check that doesn't warrant a permanent test file.

When the behavior deserves a lasting regression guard, write a real test in `tests/Feature` or `tests/Browser` instead. The Agent Browser plugin is designed to give agents fast, honest feedback while they work — not to skip the tests that keep your application healthy.

---

Next, let's dive into architectural testing and how it can help you evaluate the overall design of your application and catch potential flaws before they become significant issues: [Architecture Testing](/docs/arch-testing)
