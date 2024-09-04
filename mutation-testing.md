---
title: Mutation Testing
description: Mutation Testing is a technique used to evaluate the quality of test suites by introducing small changes (mutations) to the source code and verifying if the tests can detect these changes. This process helps developers identify weak spots in their test suites and improve the overall quality of their tests.
---

# Mutation Testing

**Requires [XDebug 3.0+](https://xdebug.org/docs/install/)** or [PCOV](https://github.com/krakjoe/pcov).

Mutation Testing is a technique used to evaluate the quality of your application's test suite by detecting "untested" code. Unlike code coverage, mutation testing is not solely about "covered" code, but more about the actual quality of the tests.

The way mutation testing works is by introducing small changes (mutations) to the source code and verifying if the tests are failing against these changes. This process helps developers identify weak spots in their test suites and improve the overall quality of their tests.

Unlike with other mutation testing libraries, our plugin is deeply integrated into Pest PHP. So, each time a mutation is introduced, Pest PHP will:

- **Only run the tests covering the mutated code** to speed up the process.
- **Cache as much as possible** to speed up the process on subsequent runs.
- If enabled, use **parallel execution to run multiple tests** in parallel to speed up the process.

To get started with mutation testing, head over to your test file, and be specific about which part of your code your test covers using the `covers()` function or the `covers()` method.

```php
<?php

covers(TodoController::class);

it('list todos', function () {
    //
});

it('create todos', function () {
    //
})->covers(CreateTodoRequest::class);
```

Finally, run Pest PHP with the `--mutate` option to start mutation testing.

```bash
./vendor/bin/pest --mutate
```

Optionally, you may run mutation testing in parallel to speed up the process. Of course, your test suite must be able to run in tests in parallel.

```bash
./vendor/bin/pest --mutate --parallel
```

After running mutation testing, you will see a detailed report showing the number of mutations, how many were caught, and the mutation score.

```
[IMAGE HERE]
```

The higher the mutation score, the better your test suite is. A mutation score of 100% means that all mutations were "covered", which is the goal of mutation testing.

Now, if you see "uncovered" mutations, or are a mutation score below 100%, typically means that you have **missing tests** or that **your tests are not covering all the edge cases**.

<a name="covered-vs-uncovered-mutations"></a>
## Covered Vs Uncovered Mutations

When running mutation testing, you will see two types of mutations: **covered** and **uncovered** mutations.

- **Covered Mutations**: These are mutations that were detected by your test suite. They are considered "covered" because your tests were able to catch the changes introduced by the mutation.

As example, the following mutation is considered "covered" because the test suite was able to detect the change.

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

- **Uncovered Mutations**: These are mutations that were not detected by your test suite. They are considered "uncovered" because your tests were not able to catch the changes introduced by the mutation.

As example, the following mutation is considered "uncovered" because the test suite was not able to detect the change.

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

Changing the return value is only one of many possible mutations. Typically, a mutation can be a change in the return value, a change in the method call, a change in the method arguments, and so on.

<a name="mutate-all"></a>
## Mutate All

The concept of mutation testing is very powerful but also very resource intensive. For that reason, we default to running only mutations on files specified by the `covers()` function or the `covers()` method.

However, you can run mutation testing on your whole project by running Pest PHP with the `--mutate-all` option without any additional options. But be aware that this can be very slow and resource intensive.

```php
./vendor/bin/pest --mutate-all
```

Again, we recommend running mutation testing on specific parts of your codebase using the `covers()` function or the `covers()` method. This way, you can focus on the most critical parts of your codebase and improve the quality of your test suite.

<a name="options"></a>
## Options

The following options are available for mutation testing:

<div class="collection-method-list" markdown="1">

[TODO]...

</div>

---

As you can see Pest PHP's mutation testing feature is a powerful tool to improve the quality of your test suite. In the following chapter, we explain how can you use Snapshots to test your code: [Snapshot Testing](/docs/snapshot-testing)
