---
title: Browser Recording
description:
---

# Browser Recording

Pest can generate browser tests automatically by recording your interactions with the application. Run the following
command to start a recording session:

```bash
vendor/bin/pest --record
```

Pest auto-starts an HTTP server with `APP_ENV=testing` — the same environment used when running browser tests — opens a
browser at full-screen resolution, and records every click, fill, navigation, selection, and assertion as you interact
with the application. When you close the browser, Pest prompts for a test description and an output file, then writes
the generated test to `tests/Browser/`.

A typical generated test looks like this:

```php
it('may create a new post', function () {
    $this->visit('/posts/create')
         ->fill('#title', 'Hello World')
         ->fill('#body', 'My first post.')
         ->click('Publish')
         ->assertSee('Post created successfully');
});
```

## Options

| Option                 | Default           | Description                                                 |
|------------------------|-------------------|-------------------------------------------------------------|
| `--url=`               | auto              | Skip the auto-server; connect to this URL instead           |
| `--visit=`             | `/`               | Path to open on start                                       |
| `--test-id-attribute=` | `id`              | HTML attribute used for element selectors                   |
| `--device=`            | —                 | Emulate a device (e.g. `"iPhone 15"`)                       |
| `--viewport=`          | screen resolution | Viewport size in pixels (e.g. `1280,800`)                   |
| `--auth` / `--user`    | —                 | Start the browser pre-authenticated as a factory user       |
| `--auth-script=`       | —                 | Path to a custom auth bootstrap script (non-Laravel apps)   |
| `--env=`               | `testing`         | Environment for the auto-started server                     |
| `--migrate-fresh`      | —                 | Run `migrate:fresh` before opening the browser              |
| `--seed`               | —                 | Seed the database after `--migrate-fresh`                   |
| `--browser=`           | `chrome`          | Browser to record with: `chrome`, `firefox`, or `safari`    |
| `--channel=`           | —                 | Browser channel (e.g. `msedge`, `chrome-canary`)            |
| `--lang=`              | —                 | Override the browser locale (e.g. `fr`, `nl`)               |
| `--timezone=`          | —                 | Override the browser timezone (e.g. `Europe/Paris`)         |
| `--color-scheme=`      | —                 | Preferred color scheme: `dark`, `light`, or `no-preference` |

## Authenticated Recording

Browser tests run against an isolated `APP_ENV=testing` database, so users that exist in your local database are not
present when the test runs. The `--auth` flag solves this without requiring a manual login flow: before the browser
opens, Pest bootstraps the Laravel app, creates a factory user, starts a session authenticated as that user, and injects
a valid session cookie into the browser — so the browser starts pre-authenticated.

```bash
vendor/bin/pest --record --auth --visit=/dashboard
```

The generated test automatically uses `actingAs` so no real credentials are needed and the test works in any
environment:

```php
it('may view the dashboard', function () {
    $this->actingAs(\App\Models\User::factory()->create());

    $this->visit('/dashboard')
         ->assertSee('Welcome back');
});
```

`--user` is an alias for `--auth`.

## Fresh Database

To record against a predictable, clean dataset, combine `--migrate-fresh` with `--seed`:

```bash
vendor/bin/pest --record --migrate-fresh --seed --visit=/dashboard
```

This runs `php artisan migrate:fresh` (and optionally `db:seed`) before the browser opens, giving you a known starting
state for your recording.

## Recording in a Specific Browser

```bash
vendor/bin/pest --record --browser=firefox
vendor/bin/pest --record --browser=chrome --channel=msedge
```

## Recording with Locale, Timezone, or Color Scheme

You can override the browser's locale, timezone, and color scheme to record tests that target specific regional or
accessibility scenarios:

```bash
vendor/bin/pest --record --lang=fr --timezone=Europe/Paris
vendor/bin/pest --record --color-scheme=dark
```

## Recording Against an Existing Server

Pass `--url=` to skip the auto-started server entirely and connect to a running instance. This is useful when you have
Laravel Herd, Valet, or an existing `php artisan serve` process:

```bash
vendor/bin/pest --record --url=http://localhost:8000 --visit=/dashboard
```

Please be aware that test usually run against an isolated `APP_ENV=testing` database, so if you connect to a running
instance with a different environment, the generated test may not work as expected without additional configuration.

---

Now, let's dive into the full browser testing API, including assertions, interactions, and
debugging: [Browser Testing](/docs/browser-testing)
