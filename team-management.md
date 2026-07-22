---
title: Team Management
description: Manage tasks and todos with your team directly from the console — create, assign, and track them without leaving your Pest test suite.
---

# Team Management

With Pest, you may manage tasks and todos with your team directly from the console. You may create, assign, and track tasks, as well as view the status of each task.

## Setting Up Project

To get started with team management in Pest, you should specify the project's URL in your `Pest.php` configuration file. This URL will be used to link todos to the corresponding project management system:

```php
pest()->project()->github('my-organization/my-repository');
```

If you are using a different version control system, you may use the `gitlab`, `bitbucket`, `jira`, or `custom` methods instead.

## Creating Todos

Typically, todos are linked to one or more tests that need to be passing. As such, tests may be used to track the progress of your todos and tasks. Pest provides a simple way to create todos by using the `todo()` method:

```php
it('has a contact page', function () {
    //
})->todo();
```

When running your tests, Pest will inform you about any tests that are todos, so you don't forget them and can see them in the test results:

<div class="terminal not-prose"><div class="l">&nbsp;&nbsp;<span class="chip todo">TODO</span>&nbsp;&nbsp;<span class="white">Tests\Feature\HomepageTest</span> <span class="gray">- 1 todo</span></div><div class="l">&nbsp;&nbsp;<span class="green">↓</span> it has home</div><div class="l">&nbsp;</div><div class="l"><span class="gray">Tests:</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="green b">1 todo</span> <span class="gray">(0 assertions)</span></div><div class="l"><span class="gray">Duration:</span>&nbsp;&nbsp;&nbsp;<span class="white">0.12s</span></div></div>

If you have one or more todos, you may wish to view them separately from the rest of your test suite. To accomplish this, you may include the `--todos` option when running Pest:

```bash
./vendor/bin/pest --todos
```

## Assigning Todos

Sometimes you may wish to assign a todo to a specific team member. Pest allows you to do this by providing their name to the `assignee` argument of the `todo()` method:

```php
it('has a contact page', function () {
    //
})->todo(assignee: 'nunomaduro');
```

You may assign multiple assignees by providing an array of names to the `assignee` argument. In addition, you may filter todos by assignee by providing their name to the `--assignee` option when running Pest:

```bash
./vendor/bin/pest --todos --assignee=nunomaduro
```

## Set Corresponding Issues

Sometimes, todos are linked to issues in your project management system. Pest allows you to set the corresponding issue on a todo by providing the issue number to the `issue` argument of the `todo()` method:

```php
it('has a contact page', function () {
    //
})->todo(issue: 123);
```

Just as with assignees, you may set multiple issues by providing an array of issue numbers to the `issue` argument. In addition, you may filter todos by issue by providing the issue number to the `--issue` option when running Pest:

```bash
./vendor/bin/pest --todos --issue=123
```

## Set Corresponding PRs

Sometimes, todos are linked to pull requests in your version control system. Pest allows you to set the corresponding pull request on a todo by providing the pull request number to the `pr` argument of the `todo()` method:

```php
it('has a contact page', function () {
    //
})->todo(pr: 123);
```

Just as with assignees, you may set multiple pull requests by providing an array of pull request numbers to the `pr` argument. In addition, you may filter todos by pull request by providing the pull request number to the `--pr` option when running Pest:

```bash
./vendor/bin/pest --todos --pr=123
```

## Writing Notes for Todos

It is often helpful to provide additional context for a todo. Pest allows you to write notes for a todo by providing a string to the `note` argument of the `todo()` method:

```php
it('has a contact page', function () {
    //
})->todo(note: <<<NOTE
    Given I am a user
    When I visit the contact page
    Then I should see a contact form
NOTE);
```

The notes will be displayed below the todo in the test results.

## Marking Todos as Work in Progress

When a todo is being worked on, you may mark it as work in progress by using the `wip()` method. This method will remove the todo status from the test and mark it as a regular test, while keeping all the context such as assignees, issues, and so on:

```php
it('has a contact page', function () {
    //
})->wip(assignee: 'nunomaduro', issue: 123);
```

## Marking Todos as Done

Once a todo is completed, you may mark it as done by using the `done()` method. This method will remove the todo status from the test and mark it as a regular test, while keeping all the context such as assignees, issues, and so on:

```php
it('has a contact page', function () {
    //
})->done(assignee: 'nunomaduro', issue: 123);
```

## Combining Todos with Assignees, Issues, and PRs

You may combine todos with assignees, issues, and PRs to provide additional context and track the progress of your todos. This may be done using the `describe` group, along with the `todo`, `assignee`, `issue`, and `pr` methods:

```php
describe('contacts', function () {
    it('has a contact page', function () {
        //
    })->issue(123); // or ->pr(123) etc
    
    it('has a contact form', function () {
        //
    })->done(pr: 567);
})->wip(assignee: 'nunomaduro');
```

---

Next, let's explore how Pest can scan your codebase for profanity, helping you keep your tests and their output professional: [Profanity](/docs/profanity)
