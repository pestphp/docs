---
title: Tia Engine
description: The Tia Engine (Test Impact Analysis) dramatically reduces the time it takes to run your test suite by re-running only the tests affected by your latest changes.
---

# Tia Engine

The **Tia Engine** — short for Test Impact Analysis — is a convenient way to reduce the time it takes to run your test suite, re-running only the tests affected by your latest changes. The first time you run with `--tia`, the engine records a graph of which tests depend on which files. On every run after that, the engine looks at what you changed, runs only the tests that touched those files, and replays cached results for everything else.

A typical Laravel suite that used to take 10 minutes now replays in around 4 seconds. Edits to a single Blade template re-run a handful of feature tests. Comment-only edits, formatter passes, and README touches re-run nothing at all.

<iframe width="100%" height="315" src="https://www.youtube.com/embed/9vt8hkAWLRM" title="the fastest testing engine in the world (pest 5, day 1/6)" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

To get started, you may add the `--tia` flag to any Pest invocation:

```bash
./vendor/bin/pest --parallel --tia
```

> **Warning:** The Tia Engine requires a code coverage driver — either [PCOV](https://github.com/krakjoe/pcov) or [Xdebug](https://xdebug.org/) — to be installed and enabled. The engine uses it to record which files each test touches while building the baseline. Without a coverage driver available, Pest cannot record the dependency graph, and TIA will not run.

The first run is the **baseline** — the engine enables a coverage driver (PCOV or Xdebug) and records the dependency graph as your tests execute. You may expect a small overhead on this run only.

> **Warning:** The Tia Engine is built for local development, and you should not add `--tia` to the command that runs your test suite on CI. Your pipeline exists to verify every test against a clean checkout, so it should always execute the full suite — the single exception is the dedicated job that records the shared baseline, described in [Sharing The Baseline From CI](#sharing-the-baseline-from-ci).

> **Note:** You don't have to pay this baseline cost on every machine. You may have CI record the baseline once and have every developer download it from GitHub Actions, so their very first `--tia` run replays immediately. See [Sharing The Baseline From CI](#sharing-the-baseline-from-ci) to set this up.

Every subsequent run is a **replay**. The engine compares your working tree against the baseline and re-runs only the tests affected by your changes:

```plain
Tests:    774 passed (2658 assertions, 7 affected, 2 uncached, 765 replayed)
Duration: 3.92s
```

In this example, `affected` is the set of tests Pest re-ran because their dependencies changed. `uncached` means Pest had to execute a test because no cached result existed yet. Finally, `replayed` is the set whose results were served from cache.

A replay isn't a shortcut that skips work — it's a faithful reconstruction of the real run. When the engine caches a test, it stores not just the pass or fail result but everything that test produced, including the exact lines and branches it covered. So a replayed run reports the same code coverage as a full run, and everything that depends on it keeps working — coverage thresholds, the `--coverage` report, and `--min` all behave exactly as if every test had executed from scratch. You get the speed of replaying with none of the fidelity lost.

## How The Engine Decides What To Run

For each file you have changed, the engine looks for the tests that depend on it:

- **PHP source files** — your `app/` classes, controllers, models, helpers — are tracked through the coverage driver. A change to `app/Models/User.php` re-runs only the tests that touched `User`.
- **Migrations** are intersected with the tables each test queried during the baseline. A column rename in `create_users_table.php` re-runs only the tests that queried the `users` table.
- **Inertia pages** under `resources/js/Pages` re-run only the tests that server-side-rendered them.
- **Shared JS components** under `resources/js/Components`, `Layouts`, and friends re-run only the tests whose pages import them — Pest walks Vite's module graph to figure this out.
- **Frontend runtime files** like `resources/js/App.jsx`, `resources/js/bootstrap.js`, `resources/js/echo.js`, and `resources/js/favicon.js` re-run tests that rendered Inertia components, because they can affect the whole client runtime.
- **Blade templates** re-run only the tests that rendered them, including renders triggered by browser tests.
- **Arch tests** re-run for project PHP source changes, because Arch expectations inspect files by namespace and path instead of executing those files.
- **Browser assets** such as CSS, public build files, static public assets, and `public/hot` re-run browser tests only.
- **Anything else** — config files, route files, fixture data, files outside the recorded graph — falls through to a broader pattern. Editing `config/app.php` re-runs every test, because Pest cannot statically prove which tests depend on it.

Some files change the shape of the graph itself rather than a single test result. Pest rebuilds the graph when structural inputs drift, including `composer.lock`, `phpunit.xml` / `phpunit.xml.dist`, `vite.config.*`, Node lockfiles (`package-lock.json`, `pnpm-lock.yaml`, `yarn.lock`, `bun.lock`, `bun.lockb`), and `tsconfig` / `jsconfig` files. Upgrading your PHP version invalidates the cached results and re-executes the suite while keeping the graph intact.

## Cosmetic Edits Don't Run Anything

Pest normalizes file content before comparing, so cosmetic changes will not trigger any tests. PHP files have their whitespace, line comments, and docblocks stripped before hashing. Blade strips `{{-- … --}}` comments. JS, TS, Vue, and Svelte files have their line and block comments removed too.

As a result, a comment-only edit, a Prettier reformat, a Pint pass, or a README tweak produces an identical hash, and the file never enters the changed set. Zero tests run.

## Built-in Environments

Pest ships with watch defaults for the most common PHP stacks, and applies them automatically when their packages are installed:

- **PHP** — always-on rules that re-run the whole suite when `.env` files (`.env`, `.env.testing`, `.env.local`, `.env.*.local`), `docker-compose.yml` / `docker-compose.yaml`, `phpunit.xml*`, your test fixtures (`tests/Fixtures/**` and nested `Fixtures` directories), or snapshot files (`tests/.pest/snapshots/**/*.snap`) change.
- **Laravel** — non-PHP files under `app/`, `database/migrations/`, `storage/fixtures/`, `resources/views/`, `lang/` and `resources/lang/`, plus build configs such as `vite.config.*`, `webpack.mix.*`, `tailwind.config.*`, and `postcss.config.*`.
- **Symfony** — `config/`, `migrations/`, `src/Migrations/`, `templates/`, `translations/`, `config/doctrine/`, `assets/`, `webpack.config.js`, and `importmap.php`.
- **Livewire** — `resources/views/livewire/`, `resources/views/components/`, `resources/views/pages/`, plus JS/TS under `resources/js/`.
- **Inertia** — server-side-rendered pages under `resources/js/Pages` and the Vite module graph for `Components`, `Layouts`, and runtime entry files.
- **Browser** — CSS, public build files, static public assets, and `public/hot` re-run browser tests only.

There is no need to configure anything to opt in to these — Pest detects each framework via Composer and merges the relevant rules for you. To extend or override them, see [Custom Watch Patterns](#custom-watch-patterns).

## Modes

Pest supports a few flags alongside `--tia`:

| Flag | Behavior |
|---|---|
| `--tia` | Replay if a baseline graph exists, otherwise record. |
| `--no-tia` | Disable TIA for this run, even if `pest()->tia()->locally()` is configured. |
| `--tia --fresh` | Discard any existing graph and re-record from scratch. Use this after large refactors or when the graph feels stale. |
| `--tia --refetch` | Discard the local graph and force a fresh CI baseline fetch, bypassing the 24-hour cooldown that otherwise applies after a fetch found no baseline. |
| `--tia --filtered` | Narrow PHPUnit to only the affected test files rather than loading all tests and replaying cached results for unaffected ones. Automatically disabled when you pass an explicit test path or a `--coverage` report; if no tests are affected, Pest stops and tells you so. |
| `--tia --locally` | Equivalent to `pest()->tia()->locally()` — run TIA automatically on local machines but skip on CI. |
| `--tia --baselined` | Opt in to fetching the shared baseline from CI when no local graph exists or the local graph drifts. |
| `--baseline` | Print the absolute path of this project's TIA storage directory and exit. Designed for CI uploads — see [Sharing The Baseline From CI](#sharing-the-baseline-from-ci). |

### Environment Variables

Each enabling flag has an environment variable equivalent, useful for CI matrices, container entry points, and shared developer configs:

| Variable | Equivalent flag |
|---|---|
| `PEST_TIA=1` | `--tia` |
| `PEST_TIA_FILTERED=1` | `--filtered` |
| `PEST_TIA_LOCALLY=1` | `--locally` |
| `PEST_TIA_BASELINED=1` | `--baselined` |

<a name="sharing-the-baseline-from-ci"></a>
## Sharing The Baseline From CI

Recording the baseline locally may take minutes on large suites. Instead, you may have CI record it once per merge to `main`, and every developer downloads the result.

Recording the baseline is the one job where `--tia` belongs on CI. It should live in a workflow of its own — the pipeline that tests your pull requests and commits continues to run the full suite with `./vendor/bin/pest --ci`, without any TIA flags.

Baseline fetching is opt-in. You may enable it with `--tia --baselined` on the command line, the `PEST_TIA_BASELINED=1` environment variable, or — preferred for teams — by calling `pest()->tia()->baselined()` in `tests/Pest.php`. Once enabled, when Pest detects no local graph (or the local graph is out of date) it uses GitHub's CLI to download the latest successful run of a `tia-baseline.yml` workflow's `pest-tia-baseline` artifact. Pest then validates the fetched graph against your project state — if it matches, it is adopted. Otherwise, it is discarded and a local rebuild proceeds.

> **Note:** Baseline fetching relies on the [GitHub CLI](https://cli.github.com/) (`gh`), so it is only available for repositories hosted on GitHub, and `gh` must be installed and authenticated (`gh auth login`) on the machine doing the fetch. When a fetch cannot proceed — missing CLI, no authentication, a network or rate-limit error, or no baseline artifact yet — Pest reports the reason and falls back to recording a local baseline.

Here is a starter workflow you may drop into `.github/workflows/tia-baseline.yml`:

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
        with: { php-version: '8.4', coverage: xdebug }
      - run: composer install --no-interaction --prefer-dist

      - name: Run tests
        run: ./vendor/bin/pest --parallel --tia --coverage --fresh

      - name: Resolve TIA baseline path
        id: baseline
        run: echo "path=$(./vendor/bin/pest --baseline)" >> "$GITHUB_OUTPUT"

      - name: Upload TIA baseline
        uses: actions/upload-artifact@v4
        with:
          name: pest-tia-baseline
          path: ${{ steps.baseline.outputs.path }}
          include-hidden-files: true
          retention-days: 30
```

The `./vendor/bin/pest --baseline` command prints the absolute path to this project's TIA storage directory (typically `~/.pest/tia/<project-key>/`), which is exactly what `actions/upload-artifact` needs to bundle the recorded graph and coverage cache. Note that `include-hidden-files: true` is required because the baseline lives under a dot-prefixed directory.

After CI runs, every developer with `baselined()` enabled who runs `./vendor/bin/pest --tia` for the first time on the repo will download this baseline and start replaying immediately, paying no record cost.

## Storage

Pest stores its state at `~/.pest/tia/<project-key>/`, where the project key is derived from your normalized git remote URL — so `git@github.com:foo/bar.git` and `https://github.com/foo/bar` produce the same key. A non-git project falls back to a hash of the project's absolute path.

Sharing state per remote URL means multiple worktrees of the same repository share one cache, while unrelated projects on the same machine stay isolated.

## Configuration

You may configure TIA behavior in `tests/Pest.php` via `pest()->tia()`:

```php
pest()->tia()
    ->locally()    // run TIA on every local invocation, no --tia flag needed
    ->baselined()  // fetch the shared baseline from CI when no local graph exists
    ->filtered();  // narrow PHPUnit to only affected test files
```

Typically, you should reach for **`locally()`**. It activates TIA for every `pest` run without requiring the `--tia` flag, and restricts that behavior to local machines — on CI, or whenever you pass the `--ci` flag, TIA is skipped automatically, so your pipeline keeps running the full suite:

```php
pest()->tia()->locally();
```

Alternatively, **`always()`** activates TIA everywhere, CI included. The two are alternatives rather than a pair, so there is no need to chain them — and because the Tia Engine is built for local development, `locally()` is the option you should prefer:

```php
pest()->tia()->always();
```

In either case, an explicit `--tia` on the command line always takes effect, and `--no-tia` will disable TIA for a single run.

**`filtered()`** enables filtered mode, equivalent to `--tia --filtered`. In this mode, Pest narrows PHPUnit to only the affected test files rather than loading the full suite and replaying cached results for unaffected tests:

```php
pest()->tia()->filtered();
```

**`baselined()`** opts in to fetching the shared TIA baseline from CI when no local graph exists or the local graph drifts. See [Sharing The Baseline From CI](#sharing-the-baseline-from-ci) for the recommended workflow:

```php
pest()->tia()->baselined();
```

<a name="custom-watch-patterns"></a>
## Custom Watch Patterns

Sometimes your project may have a directory layout that does not match the framework defaults. In that case, you may register custom watch patterns in `tests/Pest.php`:

```php
pest()->tia()->watch([
    'config/billing/**/*.php' => 'tests/Feature/Billing',
    'public/build/**/*'       => 'tests/Browser',
]);
```

Each glob maps to a test directory or an exact test file. Whenever a matching file changes, every test under that directory is invalidated. If a glob already exists in Pest's defaults, your target will be merged with the existing targets rather than replacing them.

---

Now that you've learned how to use the Tia Engine to speed up your test suite, let's explore how to automatically modernize and refactor your test code with Rector: [Rector](/docs/rector)
