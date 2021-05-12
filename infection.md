---
title: Infection Support
description: Integration between Pest and Infection
---

# Mutation Testing with Infection

- [Overview](#overview)

<a name="overview"></a>
## Overview

> [Mutation Testing](https://en.wikipedia.org/wiki/Mutation_testing) is used to design new software tests and evaluate the quality of existing software tests.

[Infection](https://infection.github.io/guide/) is a Mutation Testing library for PHP.

It has out-of-the-box support for Pest, which makes it very easy to start with.

[Install](https://infection.github.io/guide/installation.html) Infection using one of the supported method, for example as a package:

```bash
composer require infection/infection --dev
```

and run it

```bash
vendor/bin/infection --test-framework=pest --show-mutations
```

> **Note**: Required versions: Pest 1.2.0, Infection 0.23.0

---

Next section: [Laravel Plugin →](/docs/plugins/laravel)
