---
title: Mutation Testing
description: Mutation Testing evaluates the quality of your test suite by introducing small changes to your source code and checking whether your tests catch them, helping you find weak spots and improve overall quality.
---

# Mutation Testing

- **[Get Started](#get-started)**
- **[Tested vs. Untested Mutations](#tested-vs-untested-mutations)**
- **[Minimum Threshold Enforcement](#minimum-threshold-enforcement)**
- **[Options & Modifiers](#options-and-modifiers)**

<a name="get-started"></a>
## Get Started

> **Note:** Mutation testing requires [XDebug 3.0+](https://xdebug.org/docs/install/) or [PCOV](https://github.com/krakjoe/pcov).

Mutation Testing is a powerful technique that introduces small changes (mutations) to your code to see whether your tests catch them. This ensures you are testing your application thoroughly, moving beyond code coverage alone and toward the actual quality of your tests. It is a helpful way to identify weaknesses in your test suite and improve its quality.

To get started with mutation testing, head over to your test file and be specific about which part of your code your test covers using the `covers()` function or the `mutates()` function:

```php
covers(TodoController::class); // or mutates(TodoController::class);

it('list todos', function () {
    $this->getJson('/todos')->assertStatus(200);
});
```

Both the `covers()` and `mutates()` functions behave identically when it comes to mutation testing. However, `covers()` also affects the code coverage report; when provided, it filters the report to include only the executed code from the referenced code parts.

Then, run Pest with the `--mutate` option to start mutation testing. Ideally, you should also add the `--parallel` option to speed up the process:

```bash
./vendor/bin/pest --mutate
# or in parallel...
./vendor/bin/pest --mutate --parallel
```

Pest will then re-run your tests against the "mutated" code and check whether the tests are still passing. If a test still passes against a mutation, it means the test is not covering that specific part of the code. As a result, Pest will output the mutation along with the diff of the code:

```diff
UNTESTED  app/Http/TodoController.php  > Line 44: ReturnValue - ID: 76d17ad63bb7c307

class TodoController {
    public function index(): array
    {
         // pest detected that this code is untested because
         // the test is not covering the return value
-        return Todo::all()->toArray();
+        return [];
    }
}

  Mutations: 1 untested
  Score:     33.44%
```

Once you have identified the untested code, you may write additional tests to cover it:

```diff
covers(TodoController::class);

it('list todos', function () {
+    Todo::factory()->create(['name' => 'Buy milk']);

-    $this->getJson('/todos')->assertStatus(200);
+    $this->getJson('/todos')->assertStatus(200)->assertJson([['name' => 'Buy milk']]);
});
```

Then, you may re-run Pest with the `--mutate` option to see whether the mutation is now "tested" and covered:

```bash
  Mutations: 1 tested
  Score:     100.00%
```

The higher the mutation score, the better your test suite. A mutation score of 100% means that all mutations were "tested", which is the goal of mutation testing.

A mutation score below 100%, along with "untested" or "uncovered" mutations, typically means that you have missing tests or that your tests are not covering all of the edge cases.

Mutation testing is deeply integrated into Pest, so each time a mutation is introduced, Pest will:

- **Only run the tests covering the mutated code** to speed up the process.
- **Cache as much as possible** to speed up the process on subsequent runs.
- If enabled, use **parallel execution to run multiple tests** in parallel to speed up the process.

<a name="tested-vs-untested-mutations"></a>
## Tested vs. Untested Mutations

When running mutation testing, you will mainly see two types of mutations: tested and untested mutations.

- **Tested Mutations**: These are mutations that were detected by your test suite. They are considered "tested" because your tests were able to catch the changes introduced by the mutation.

For example, the following mutation is considered "tested" because the test suite was able to detect the change:

```diff
class TodoController
{
    public function index(): array
    {
-        return Todo::all()->toArray();
+        return [];
    }
}

it('list todos', function () {
    Todo::factory()->create(['name' => 'Buy milk']);

    // this fails because the mutation changed the return value, proving that the test is working and testing the return value...
    $this->getJson('/todos')->assertStatus(200)->assertJsonContains([
        ['name' => 'Buy milk'],
    ]);
});
```

- **Untested Mutations**: These are mutations that were not detected by your test suite. They are considered "untested" because your tests were not able to catch the changes introduced by the mutation.

For example, the following mutation is considered "untested" because the test suite was not able to detect the change:

```diff
class TodoController
{
    public function index(): array
    {
-        return Todo::all()->toArray();
+        return [];
    }
}

it('list todos', function () {
    Todo::factory()->create(['name' => 'Buy milk']);

    // this test still passes even though the return value was changed by the mutation...
    $this->getJson('/todos')->assertStatus(200);
});
```

Changing the return value is only one of many possible mutations. Typically, a mutation may be a change in the return value, a change in the method call, a change in the method arguments, and so on.

<a name="minimum-threshold-enforcement"></a>
## Minimum Threshold Enforcement

To ensure comprehensive testing and maintain testing quality, you should set minimum threshold values for your mutation testing results. In Pest, you may use the `--mutate` and `--min` options to define the minimum threshold for your mutation score. If the specified thresholds are not met, Pest will report a failure:

```bash
./vendor/bin/pest --mutate --min=40
```

<img src="/assets/mutation-testing-min.png" style="width: 100%;" />

<a name="options-and-modifiers"></a>
## Options & Modifiers

The following options and modifiers are available when running mutation testing:

<div class="collection-method-list" markdown="1">

- [`@pest-mutate-ignore`](#pest-mutate-ignore)
- [`--id`](#id)
- [`--everything`](#everything)
- [`--covered-only`](#covered-only)
- [`--bail`](#bail)
- [`--class`](#class)
- [`--ignore`](#ignore)
- [`--clear-cache`](#clear-cache)
- [`--no-cache`](#no-cache)
- [`--ignore-min-score-on-zero-mutations`](#ignore-min-score-on-zero-mutations)
- [`--profile`](#profile)
- [`--retry`](#retry)
- [`--stop-on-uncovered`](#stop-on-uncovered)
- [`--stop-on-untested`](#stop-on-untested)

</div>

<a name="pest-mutate-ignore"></a>
### `@pest-mutate-ignore`

Ignore the given line of code when generating mutations:

```php
public function rules(): array
{
    return [
        'name' => 'required',
        'email' => 'required|email', // @pest-mutate-ignore
    ];
}
```

> **Note:** Lines that are not considered executable will always be marked as UNCOVERED.

For such cases, like with model properties, you may apply `@pest-mutate-ignore` in the following way:

```php
/**
 * @pest-mutate-ignore
 */
protected $guarded = [
    'id',
    'created_at',
    'updated_at',
];

/**
 * @pest-mutate-ignore
 */
protected $hidden = [
    'id',
    'created_at',
    'updated_at',
];
```

<a name="id"></a>
### `--id`

Run only the mutation with the given ID. Note that you will need to provide the same options as the original run:

```bash
./vendor/bin/pest --mutate --id=ecb35ab30ffd3491
```

<a name="everything"></a>
### `--everything`

Generate mutations for all of your project's classes, bypassing the `covers()` method. This option is resource-intensive and should be combined with the `--covered-only` option:

```bash
./vendor/bin/pest --mutate --everything --parallel --covered-only
```

Ideally, you should also add the `--parallel` option to speed up the process.

<a name="covered-only"></a>
### `--covered-only`

Only generate mutations in the lines of code that are covered by tests:

```bash
./vendor/bin/pest --mutate --covered-only
```

<a name="bail"></a>
### `--bail`

Stop mutation testing execution upon the first untested or uncovered mutation:

```bash
./vendor/bin/pest --mutate --bail
```

<a name="class"></a>
### `--class`

Generate mutations for the given class(es). For example, `--class=App\Models`:

```bash
./vendor/bin/pest --mutate --class=App\Models
```

<a name="ignore"></a>
### `--ignore`

Ignore the given class(es) when generating mutations. For example, `--ignore=App\Http\Requests`:

```bash
./vendor/bin/pest --mutate --ignore=App\Http\Requests
```

<a name="clear-cache"></a>
### `--clear-cache`

Clear the mutation cache and run mutation testing from scratch:

```bash
./vendor/bin/pest --mutate --clear-cache
```

<a name="no-cache"></a>
### `--no-cache`

Run mutation testing without using cached mutations:

```bash
./vendor/bin/pest --mutate --no-cache
```

<a name="ignore-min-score-on-zero-mutations"></a>
### `--ignore-min-score-on-zero-mutations`

Ignore the minimum score requirement when there are no mutations:

```bash
./vendor/bin/pest --mutate --min=80 --ignore-min-score-on-zero-mutations
```

<a name="profile"></a>
### `--profile`

Output the top ten slowest mutations to standard output:

```bash
./vendor/bin/pest --mutate --profile
```

<a name="retry"></a>
### `--retry`

Run untested or uncovered mutations first and stop execution upon the first error or failure:

```bash
./vendor/bin/pest --mutate --retry
```

<a name="stop-on-uncovered"></a>
### `--stop-on-uncovered`

Stop mutation testing execution upon the first uncovered mutation:

```bash
./vendor/bin/pest --mutate --stop-on-uncovered
```

<a name="stop-on-untested"></a>
### `--stop-on-untested`

Stop mutation testing execution upon the first untested mutation:

```bash
./vendor/bin/pest --mutate --stop-on-untested
```

---

As you can see, Pest's mutation testing feature is a powerful tool for improving the quality of your test suite. Next, let's explore how the Tia Engine speeds up your suite by re-running only the tests affected by your latest changes: [Tia Engine](/docs/tia)
