---
title: Test Impact Analysis (TIA)
description: Test Impact Analysis is a great way to drastically reduce the time it takes to run your test suite by re-running only the tests affected by your latest changes.
---

# Test Impact Analysis (TIA)

Test Impact Analysis is a great way to drastically reduce the time it takes to run your test suite by re-running only the tests affected by your latest changes. The first time you run with `--tia`, Pest records a graph of which tests depend on which files. Every run after that, Pest looks at what you changed, runs only the tests that touched those files, and replays cached results for everything else.

A typical Laravel suite that takes 15 seconds replays in under a second. Edits to a single Blade template re-run a handful of feature tests. Comment-only edits, formatter passes, and README touches re-run nothing at all.

To get started, just add the `--tia` flag to any Pest invocation:

```bash
./vendor/bin/pest --parallel --tia
```

The first run is the **baseline** — Pest enables a coverage driver (PCOV or Xdebug) and records the dependency graph as your tests execute. Expect a small overhead on this run only.

Every subsequent run is a **replay**. Pest compares your working tree against the baseline and re-runs only the tests affected by your changes:

```
Tests:    774 passed (2658 assertions, 7 affected, 767 replayed)
Duration: 0.74s
```

`affected` is the set of tests Pest actually executed. `replayed` is the set whose results were served from cache.

## How Pest Decides What To Run

For each file you've changed, Pest looks for tests that depend on it:

- **PHP source files** — your `app/` classes, controllers, models, helpers — are tracked through the coverage driver. A change to `app/Models/User.php` re-runs only the tests that touched `User`.
- **Migrations** are intersected with the tables each test queried during the baseline. A column rename in `create_users_table.php` re-runs only the tests that queried the `users` table.
- **Inertia pages** under `resources/js/Pages` re-run only the tests that server-side-rendered them.
- **Shared JS components** under `resources/js/Components`, `Layouts`, and friends re-run only the tests whose pages import them — Pest walks Vite's module graph to figure this out.
- **Blade templates** re-run only the tests that rendered them, including renders triggered by browser tests.
- **Anything else** — config files, route files, `.env`, files outside the recorded graph — falls through to a broader pattern. Editing `config/app.php` re-runs every test, because Pest can't statically prove which tests depend on it.

## Cosmetic Edits Don't Run Anything

Pest normalises file content before comparing, so cosmetic changes don't trigger any tests. PHP files have whitespace, line comments, and docblocks stripped before hashing. Blade strips `{{-- … --}}` comments. JS, TS, Vue, and Svelte files have line and block comments removed too.

The result: a comment-only edit, a Prettier reformat, a Pint pass, or a README tweak produces an identical hash, and the file never enters the changed set. Zero tests run.

## Modes

Pest supports a few flags alongside `--tia`:

| Flag | Behaviour |
|---|---|
| `--tia` | Replay if a baseline graph exists, otherwise record. |
| `--tia --fresh` | Discard any existing graph and re-record from scratch. Use this after large refactors or when the graph feels stale. |
| `--tia --refetch` | Force a CI baseline fetch even within the 24-hour cooldown after a previous failed fetch.

## Sharing The Baseline From CI

Recording the baseline locally takes minutes on large suites. Instead, you can have CI record it once per merge to `main`, and every developer downloads the result.

When Pest detects no local graph (or the local graph is out of date), it uses GitHub's CLI to download the latest successful run of a `tia-baseline.yml` workflow's `pest-tia-baseline` artifact. Pest validates the fetched graph against your project state — if it matches, it's adopted. Otherwise it's discarded and a local rebuild proceeds.

Here's a starter workflow you can drop into `.github/workflows/tia-baseline.yml`:

```yaml
name: TIA Baseline
on:
  push: { branches: [main] }
  schedule: [{ cron: '0 3 * * *' }]
  workflow_dispatch:
jobs:
  baseline:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with: { fetch-depth: 0 }
      - uses: shivammathur/setup-php@v2
        with: { php-version: '8.5', coverage: xdebug }
      - run: composer install --no-interaction --prefer-dist
      - run: ./vendor/bin/pest --parallel --tia --coverage
      - name: Stage baseline for upload
        shell: bash
        run: |
          mkdir -p .pest-tia-baseline
          cp -R "$HOME/.pest/tia"/*/. .pest-tia-baseline/
      - uses: actions/upload-artifact@v4
        with:
          name: pest-tia-baseline
          path: .pest-tia-baseline/
          retention-days: 30
```

After CI runs, every developer who runs `pest --tia` for the first time on the repo will download this baseline and start replaying immediately, paying no record cost.

## Storage

Pest stores its state at `~/.pest/tia/<project-key>/`, where the project key is derived from your normalised git remote URL — so `git@github.com:foo/bar.git` and `https://github.com/foo/bar` produce the same key. A non-git project falls back to a hash of the project's absolute path.

Sharing state per remote URL means multiple worktrees of the same repository share one cache, while unrelated projects on the same machine stay isolated.

## Custom Watch Patterns

If your project has a directory layout that doesn't match the framework defaults, you can register custom watch patterns in `tests/Pest.php`:

```php
pest()->tia()->watch([
    'config/billing/**/*.php' => ['tests/Feature/Billing'],

    //
]);
```

Each glob maps to a list of test directories; whenever a matching file changes, every test under those directories is invalidated. Use this when you have specific test directories that genuinely depend on a file Pest can't see otherwise.

---

Now that you've learned how to use Test Impact Analysis to speed up your test suite, let's discuss how to integrate Pest with your continuous integration workflow: [Continuous Integration](/docs/continuous-integration)
