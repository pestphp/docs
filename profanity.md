---
title: Profanity
description: Pest's Profanity plugin scans your codebase for profanity in places like comments, constants, and properties, helping you keep things professional.
---

# Profanity

**Source code**: [github.com/pestphp/pest-plugin-profanity](https://github.com/pestphp/pest-plugin-profanity)

The Profanity plugin scans your codebase for profanity in places like comments, constants, and properties, helping you maintain a more professional and respectful codebase. As developers, we've all faced moments of frustration — whether debugging a persistent issue or deciphering confusing code written by someone else. Those moments can sometimes lead to profanity slipping into your code.

To get started, require the plugin via Composer:

```bash
composer require pestphp/pest-plugin-profanity --dev
```

Once the plugin is installed, you may use the `--profanity` option to generate a report of your profanity:

```bash
./vendor/bin/pest --profanity
```

The Profanity plugin does not require you to write any tests. Instead, it analyzes your codebase and generates a report of your profanity. This report will display a list of files and their corresponding profanity results:

<div class="terminal not-prose"><div class="l row"><span class="white">app/Providers/AppServiceProvider.php</span><span class="lead"></span><span class="green">OK</span></div><div class="l row"><span class="white">app/Models/User.php</span><span class="lead"></span><span class="green">OK</span></div><div class="l row"><span class="white">app/Http/Requests/Auth/LoginRequest.php</span><span class="lead"></span><span class="green">OK</span></div><div class="l row"><span class="white">app/Http/Requests/ProfileUpdateRequest.php</span><span class="lead"></span><span class="red">pr20(shit)</span></div><div class="l row"><span class="white">app/Http/Controllers/Controller.php</span><span class="lead"></span><span class="green">OK</span></div><div class="l row"><span class="white">app/Http/Controllers/Auth/NewPasswordController.php</span><span class="lead"></span><span class="green">OK</span></div><div class="l row"><span class="white">app/Http/Controllers/Auth/EmailVerificationPromptController.php</span><span class="lead"></span><span class="green">OK</span></div><div class="l row"><span class="white">app/Http/Controllers/Auth/VerifyEmailController.php</span><span class="lead"></span><span class="green">OK</span></div><div class="l row"><span class="white">app/Http/Controllers/Auth/PasswordResetLinkController.php</span><span class="lead"></span><span class="green">OK</span></div><div class="l row"><span class="white">app/Http/Controllers/Auth/PasswordController.php</span><span class="lead"></span><span class="red">pr18(dogshit)</span></div><div class="l row"><span class="white">app/Http/Controllers/Auth/EmailVerificationNotificationController.php</span><span class="lead"></span><span class="green">OK</span></div><div class="l row"><span class="white">app/Http/Controllers/Auth/AuthenticatedSessionController.php</span><span class="lead"></span><span class="green">OK</span></div><div class="l row"><span class="white">app/Http/Controllers/Auth/RegisteredUserController.php</span><span class="lead"></span><span class="green">OK</span></div><div class="l row"><span class="white">app/Http/Controllers/Auth/ConfirmablePasswordController.php</span><span class="lead"></span><span class="green">OK</span></div><div class="l row"><span class="white">app/Http/Controllers/ProfileController.php</span><span class="lead"></span><span class="red">pr19(fuck), pr46(shit)</span></div><div class="l row"><span class="white">app/View/Components/GuestLayout.php</span><span class="lead"></span><span class="green">OK</span></div><div class="l row"><span class="white">app/View/Components/AppLayout.php</span><span class="lead"></span><span class="green">OK</span></div><div class="l row"><span class="white">app/Console/Commands/ListUsersCommand.php</span><span class="lead"></span><span class="red">pr36(bastard)</span></div><div class="l">&nbsp;</div><div class="l">&nbsp;&nbsp;<span class="chip fail">ERROR</span>&nbsp;&nbsp;<span class="gray">Found 5 instances of profanity in 4 files</span></div></div>

If any of your files contain profanity, they will be highlighted in red and displayed with their respective line numbers and the profane word(s) that were found.

For example, `pr31(fuck)` means that the word "fuck" was found on line 31.

## Specific Language

Often, a codebase is written in a single language, so you may wish to flag profanity only for that language. To do this, you may use the `--language` option:

```bash
./vendor/bin/pest --profanity --language=en
```

If needed, you may also pass in multiple comma-separated languages:

```bash
./vendor/bin/pest --profanity --language=en,da
```

The supported languages are `ar`, `da`, `en`, `es`, `it`, `ja`, `nl`, `pt_BR`, and `ru`. By default, `en` is used if no language is specified.

## Include Words

Sometimes you may wish to flag certain words specific to your application as profane. To do this, you may use the `--include` option:

```bash
./vendor/bin/pest --profanity --include=elephpant
```

## Exclude Words

Conversely, you may wish to exclude certain words from being flagged as profane. To do this, you may use the `--exclude` option:

```bash
./vendor/bin/pest --profanity --exclude=elephpant
```

## Compact Output

Often, when checking for profanity, you only want to see the files that actually contain it. To do this, you may use the `--compact` option:

```bash
./vendor/bin/pest --profanity --compact
```

## Different Formats

In addition, Pest may report the profanity results to a specific file:

```bash
./vendor/bin/pest --profanity --output=my-report.json
```

## Exclude Lines

Sometimes you may wish to exclude certain lines that contain profanity from being flagged, without excluding words from the whole application. In these cases, you may tell the checker to ignore specific lines:

```php
const string Fuck; // @pest-ignore-profanity
```

---

In this chapter, we have discussed Pest's Profanity plugin and how it helps you maintain a professional codebase. In the next chapter, we will explore the additional CLI options that Pest provides: [CLI API Reference](/docs/cli-api-reference)
