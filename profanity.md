---
title: Profanity
description: Pest's Profanity plugin scans your codebase for profanity in places like comments, constants, and properties, helping you keep things professional.
---


# Profanity

**Source code**: [github.com/pestphp/pest-plugin-profanity](https://github.com/pestphp/pest-plugin-profanity)

The Profanity plugin scans your codebase for profanity in places like comments, constants, and properties, helping you
maintain a more professional and respectful codebase. As developers, we've all faced moments of frustration — whether
debugging a persistent issue or deciphering confusing code written by someone else. Those moments can sometimes lead to
profanity slipping into your code.

To get started, require the plugin via Composer:

```bash
composer require pestphp/pest-plugin-profanity --dev
```

Once the plugin is installed, you may use the `--profanity` option to generate a report of your profanity:

```bash
./vendor/bin/pest --profanity
```

The Profanity plugin does not require you to write any tests. Instead, it analyzes your codebase and generates a report
of your profanity. This report will display a list of files and their corresponding profanity results:

<img src="/assets/img/profanity.png" style="width: 100%;" />

If any of your files contain profanity, they will be highlighted in red and displayed with their respective line
numbers and the profane word(s) that were found.

For example, `pr31(fuck)` means that the word "fuck" was found on line 31.

## Specific Language

Often, a codebase is written in a single language, so you may wish to flag profanity only for that language. To
do this, you may use the `--language` option:

```bash
./vendor/bin/pest --profanity --language=en
```

If needed, you may also pass in multiple comma-separated languages:

```bash
./vendor/bin/pest --profanity --language=en,da
```

We currently support: `ar`, `da`, `en`, `es`, `it`, `ja`, `nl`, `pt_BR` and `ru`. If no language is specified,
we use `en` as the default.

## Include Words

Sometimes you may wish to flag certain words specific to your application as profane. To do this, you may use
the `--include` option:

```bash
./vendor/bin/pest --profanity --include=elephpant
```

## Exclude Words

Conversely, you may wish to exclude certain words from being flagged as profane. To do this, you may use the
`--exclude` option:

```bash
./vendor/bin/pest --profanity --exclude=elephpant
```

## Compact Output

Often, when checking for profanity, you only want to see the files that actually contain it. To do this, you may use
the `--compact` option:

```bash
./vendor/bin/pest --profanity --compact
```

## Different Formats

In addition, Pest may report the profanity results to a specific file:

```bash
./vendor/bin/pest --profanity --output=my-report.json
```

## Exclude Lines

Sometimes you may wish to exclude certain lines that contain profanity from being flagged, without excluding words from
the whole application. In these cases, you may tell the checker to ignore specific lines:

```php
const string Fuck; // @pest-ignore-profanity
```

---

In this chapter, we have discussed Pest's Profanity plugin and how it helps you maintain a professional
codebase. In the next chapter, we will explore the additional CLI options that Pest
provides: [CLI API Reference](/docs/cli-api-reference)
