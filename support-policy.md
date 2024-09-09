---
title: Support Policy
description: We strive to resolve all reported bugs or issues to the best of our abilities as an open-source project. Nevertheless, we cannot ensure a fixed resolution time or guarantee the availability of a fix for every problem.
---
# Support Policy

We strive to resolve all reported bugs or issues to the best of our abilities as an open-source project. Nevertheless, we cannot ensure a fixed resolution time or guarantee the availability of a fix for every problem.

Bug fixes will be available for outdated versions for a duration of two years following the latest version's release. The previous version will be regarded as outdated once a new version of Pest is released.

| Major Version | PHP Compatibility | Initial Release   | Bug Fixes Until
| ---------------- | --- |-------------------| --- |
| Pest 3 | >= PHP 8.2 | September 9, 2024 | To be determined
| Pest 2 | >= PHP 8.1 | March 20, 2023    | September 9, 2026
| Pest 1 | >= PHP 7.3 | January 7, 2021   | March 20, 2025

Pest adheres to semantic versioning principles, where the version number `x.y.z` conveys the following information:
- When issuing bug fixes, the `z` number is incremented (e.g., 3.10.2 to 3.10.3).
- When adding new non-breaking features or improvements, the `y` number is incremented (e.g., 3.10.2 to 3.12.0).
- When introducing breaking changes, the `x` number is incremented (e.g., 3.10.2 to 4.0.0).

As maintainers of testing frameworks, we take the matter of breaking changes very seriously. Therefore, we always attempt to deliver incredible and robust new features without causing any disruption to the community's test suites. This is precisely why upgrading from Pest 1 to Pest 2 merely required updating your composer.json file, and we assure you that the process will be just as seamless when Pest v3 is released.

----

In the next chapter, we will explore the process of upgrading between major versions via our upgrade guide: [Upgrade Guide](/docs/upgrade-guide)
