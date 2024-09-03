---
title: Mutation Testing
description: Mutation Testing is a technique used to evaluate the quality of test suites by introducing small changes (mutations) to the source code and verifying if the tests can detect these changes. This process helps developers identify weak spots in their test suites and improve the overall quality of their tests.
---

# Mutation Testing

**Requires [XDebug 3.0+](https://xdebug.org/docs/install/)** or [PCOV](https://github.com/krakjoe/pcov).

Mutation Testing is a technique used to evaluate the quality of test suites by introducing small changes (mutations) to the source code and verifying if the tests can detect these changes. This process helps developers identify weak spots in their test suites and improve the overall quality of their tests and application code.

<iframe width="560" height="315" src="https://www.youtube.com/embed/SaMoPZwdOCY?si=KBskkVWLUUSyK0u0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

Unlike with other mutation testing libraries, in Pest PHP the mutation testing is deeply integrated. Which makes it very easy to use mutation testing into your daily workflow.

To start using mutation testing, you need to require the plugin via composer:

```bash
composer require pestphp/pest-plugin-mutate --dev
```

After requiring the plugin, you may start using it in two different ways:

- Using [the `--mutate` option](#the-mutate-option): It's useful when you want to run mutation testing on a specific file or directory in your project.
- Using [the `mutate()` function](#the-mutate-function): It's most effective while you are writing a test and want to run mutation testing for the given test or test file.

**Unsure which option to use?** Checkout the video above to see both options in action or to get a first impression of mutation testing in your project use the following command:

```bash
vendor/bin/pest --mutate --class=App\\Actions\\MyClass
```

Where `App\\Actions\\MyClass` is the class you want to test. As a start you will have the best experience by running mutation testing on a single class.
Choose a class with some core business logic which you already have tests for.

<a name="understand-mutation-testing"></a>
## Understand mutation testing

The concept of mutation testing is different from other testing techniques. It's not primarily about testing your application code.

It's about verifying if your test suite is able to detect changes in your application code. These changes are called mutations.
When the test suite is able to detect a mutation, the mutation is considered `caught`. If the test suite is not able to detect a mutation, the mutation is considered `escaped`.
The more mutations are caught, the better your test suite and the higher the mutation score is.

In contrast to [code coverage](/docs/test-coverage), mutation testing is not solely about how much code is executed, but about the quality of the tests.

As an example, consider the following code:

```php
class User
{
    public function canEditPost(Post $post): bool
    {
        return $post->author_id === $this->id || $this->is_admin;
    }
}
```

And the following test:

```php
test('user is allow to edit post', function () {
    $user = new User(['id' => 1]);
    $post = new Post(['author_id' => 1]);

    expect($user->canEditPost($post))->toBeTrue();
});
```

If you run mutation testing on the `User` class the mutation testing framework can introduce multiple changes (mutations). For example it could change the `===` operator to `!==`.

```diff
class User
{
    public function canEditPost(Post $post): bool
    {
-        return $post->author_id === $this->id || $this->is_admin;
+        return $post->author_id !== $this->id || $this->is_admin;
    }
}
```

After performing the mutation the test suite does fail and the mutation is considered `caught`.

Another change the mutation testing framework could introduce is to remove the `|| $this->is_admin` part.

```diff
class User
{
    public function canEditPost(Post $post): bool
    {
-        return $post->author_id === $this->id || $this->is_admin;
+        return $post->author_id === $this->id;
    }
}
```

In this case the mutation is not `caught` and the test suite does not fail because there is no test for this scenario. The mutation is considered `escaped`.

When a mutation is `escaped` this often indicates that there is a missing test case or expectation in your test suite. But sometimes it can also indicate that there is for example redundant code or code that is not needed.
So think about the mutation and if it makes sense to adjust your code or your test suite.

In the example above [code coverage](/docs/test-coverage) would not be able to detect the missing test case and would show 100% coverage. But mutation testing is able to detect the missing test case and shows a lower mutation score of 50% in this case.

To start mutation testing you always need a passing test suite. If your test suite is not passing, the mutation testing will exit after running the initial test suite before introducing any mutations.

Mutations are introduced in a way that they are not visible in the source code. This means that you will not see the mutations in your editor or in your git diff. And it does not prevent you from editing a file while mutation testing is running.

The library contains hundreds of mutators, each responsible for a specific kind of mutation. You can find a list of all mutators on [GitHub](https://github.com/pestphp/pest-plugin-mutate?tab=readme-ov-file#mutator-reference).

<a name="performance-considerations"></a>
### Performance considerations

The concept of mutation testing is very powerful but also very resource intensive.

For every mutation performed, the test suite is executed over and over again, because every mutation is tested in isolation.

If you just run `vendor/bin/pest --mutate` without any options, Pest PHP will run mutation testing on your whole project. Create thousands of mutations and run your test suite thousands of times!

The mutation testing framework does its best to optimize the process by running only test cases covering the mutated code and cache as much as possible. But it's still a very resource intensive process.

To get the best experience with mutation testing, you should start with a small part of your project. For example a [single class](#option-class) or a [single file or directory](#option-path). And you can enforce [parallel execution](#options-path) to speed up the process.

<a name="the-mutate-option"></a>
## The mutate option

To turn on mutation testing you can use the `--mutate` option.

```bash
vendor/bin/pest --mutate
```

Without any additional options Pest PHP will run mutation testing on your whole project. Or if available with the default [global configuration](#global-configuration) from your `Pest.php` file.

<a name="the-mutate-function"></a>
## The mutate function

The `mutate()` function is a helper function used in your test file to run mutation testing for a specific test or the whole test file.

When you add the `mutate()` function on top of your test file, Pest PHP will run mutation testing with all tests in the given test file.

```php
mutate();

it('does ...', function () {
    // ...
});
```

If you want to run mutation testing for a specific test, you can append the `mutate()` function directly to one or more tests.

```php
it('does ...', function () {
    // ...
})->mutate();
```

In both cases when you run Pest PHP it will automatically run mutation testing. Passing the `--mutate` option is not necessary.

```bash
vendor/bin/pest
```

In both cases the mutate function gives you the ability to chain further [options](#options).

```php
mutate()
    ->class('App\\Actions\\MyClass')
    ->parallel();

it('does ...', function () {
    // ...
});
```

<a name="options"></a>
## Options

The following options are available for mutation testing:

<div class="collection-method-list" markdown="1">

- [`parallel()`](#option-parallel)
- [`class()`](#option-class)
- [`path()`](#option-path)
- [`ignore()`](#option-ignore)
- [`mutator()`](#option-mutator)
- [`except()`](#option-except)
- [`coveredOnly()`](#option-covered-only)
- [`uncommittedOnly()`](#option-uncommittedO^-only)
- [`changedOnly()`](#option-changed-only)
- [`stopOnEscaped()`](#option-stop-on-escaped)
- [`stopOnNotCovered()`](#option-stop-on-not-covered)
- [`bail()`](#option-bail)
- [`retry()`](#option-retry)
- [`min()`](#option-min)
- [`ignoreMinScoreOnZeroMutations()`](#option-ignore-min-score-on-zero-mutations)
- [`noCache()`](#option-no-cache)
- [`--clear-cache`](#option-clear-cache)
- [`--id`](#option-id)

</div>

All options are available on the command line and as methods on the `mutate()` function (except `--clear-cache` and `--id`).

Options you want to always use can be set in the [global configuration](#global-configuration) in your `Pest.php` file.

<a name="option-parallel"></a>
### Run tests in parallel

Runs the test suite against multiple mutations in parallel. This can significantly reduce the time it takes to run mutation testing.

> Against a single mutation the tests are not run in parallel, regardless of the parallel option.

CLI
```bash
vendor/bin/pest --mutate --parallel
```

PHP
```php
mutate()->parallel();
```

By default Pest PHP will use the number of available CPU cores to determine the number of parallel processes. You can adjust the number of parallel processes with the `--processes` option.

CLI
```bash
vendor/bin/pest --mutate --parallel --processes=4
```

PHP
```php
mutate()->parallel()->processes(4);
```

<a name="option-class"></a>
### Run mutation testing for a specific class

Limit the mutations to one or more classes by providing one or multiple class names.

CLI
```bash
vendor/bin/pest --mutate --class="App\\Actions\\MyClass"
# or
vendor/bin/pest --mutate --class="App\\Actions\\MyClass,App\\Actions\\AnotherClass"
```

PHP
```php
use App\Actions\MyClass;
use App\Actions\AnotherClass;

mutate()->class(MyClass::class);
// or
mutate()->class(MyClass::class, AnotherClass::class);
```

### TODO: All remaining options ...

<a name="global-configuration"></a>
## Global Configuration

To avoid repeating the same options over and over again, you can set the options in the `Pest.php` file.

```php
mutate()
    ->parallel()
    ->paths('app', 'modules');
```

For all the available options see [Options](#options) section.

### Alternative configuration profiles

You can create multiple mutation testing configurations and give them a name.

```php
mutate('actions') // 'default' if not provided
    ->parallel()
    ->paths('app/Actions');
```

You can then run mutation testing with the given configuration profile.

```bash
vendor/bin/pest --mutate=actions
```
