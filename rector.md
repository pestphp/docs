---
title: Rector
description: Pest's Rector plugin provides automated refactoring rules powered by Rector to modernize your test code and upgrade between Pest versions.
---

# Rector

**Source code**: [github.com/pestphp/pest-plugin-rector](https://github.com/pestphp/pest-plugin-rector)

Pest's Rector plugin provides automated refactoring rules powered by [Rector](https://getrector.org/). It helps simplify and modernize your test code, and upgrade between major Pest versions.

To get started, require the plugin via Composer and install Rector:

```bash
composer require pestphp/pest-plugin-rector --dev
composer require rector/rector --dev
```

---

## Rule Sets

The plugin groups its rules into predefined sets, so that you may enable exactly the transformations you need. You may register these sets in your project's `rector.php` file, and combine as many of them as you wish.

### Pest Code Quality

The `PEST_CODE_QUALITY` set rewrites raw PHP assertions into Pest's expressive, built-in matchers, and simplifies redundant expectation patterns. For example, it converts `expect(count($array))->toBe(3)` into `expect($array)->toHaveCount(3)`.

Typically, you should pair it with the `PEST_CHAIN` set, which merges consecutive expectations on the same value into a single, fluent chain:

```php
use Pest\Rector\Set\PestSetList;
use Rector\Config\RectorConfig;

return RectorConfig::configure()
    ->withPaths([__DIR__ . '/tests'])
    ->withSets([
        PestSetList::PEST_CODE_QUALITY,
        PestSetList::PEST_CHAIN,
    ]);
```

In addition to merging consecutive `expect()` calls, the `PEST_CHAIN` set orders type checks first within each chain, so that your assertions read from the most general to the most specific. It is best applied alongside `PEST_CODE_QUALITY`, so that the matchers introduced by that set may be chained together as well.

### PHPUnit To Pest Migration

The `PEST_MIGRATION` set converts PHPUnit assertion methods and `expectException()` patterns into Pest's `expect()` API. These are structural transformations, so you should review the result once they have been applied:

```php
use Pest\Rector\Set\PestSetList;
use Rector\Config\RectorConfig;

return RectorConfig::configure()
    ->withPaths([__DIR__ . '/tests'])
    ->withSets([
        PestSetList::PEST_MIGRATION,
    ]);
```

### Laravel

The `PEST_LARAVEL` set converts `Illuminate\Support\Str` equality checks into Pest's string case matchers, such as `toBeSnakeCase()` and `toBeKebabCase()`. This set requires the `illuminate/support` package:

```php
use Pest\Rector\Set\PestSetList;
use Rector\Config\RectorConfig;

return RectorConfig::configure()
    ->withPaths([__DIR__ . '/tests'])
    ->withSets([
        PestSetList::PEST_LARAVEL,
    ]);
```

### Browser

The `PEST_BROWSER` set converts generic `expect($page->getter())->matcher()` patterns into the dedicated assertions provided by the [Browser Testing](/docs/browser-testing) plugin, resulting in more readable tests and clearer failure messages. This set requires the `pestphp/pest-plugin-browser` package:

```php
use Pest\Rector\Set\PestSetList;
use Rector\Config\RectorConfig;

return RectorConfig::configure()
    ->withPaths([__DIR__ . '/tests'])
    ->withSets([
        PestSetList::PEST_BROWSER,
    ]);
```

### Pest Version Upgrades

To upgrade your test suite between major Pest versions, you may reach for the level sets provided by `PestLevelSetList`. Unlike the sets above, a level set is cumulative: it applies every migration up to and including the version you target, so that a suite on an older version arrives fully up to date in a single pass.

```php
use Pest\Rector\Set\PestLevelSetList;
use Rector\Config\RectorConfig;

// Upgrade from Pest v2 to v3
return RectorConfig::configure()
    ->withPaths([__DIR__ . '/tests'])
    ->withSets([PestLevelSetList::UP_TO_PEST_30]);
```

To upgrade to Pest v4, target `UP_TO_PEST_40` instead. As it is cumulative, it includes every transformation from `UP_TO_PEST_30` as well, so you may upgrade from either Pest v2 or v3 in one step:

```php
use Pest\Rector\Set\PestLevelSetList;
use Rector\Config\RectorConfig;

return RectorConfig::configure()
    ->withPaths([__DIR__ . '/tests'])
    ->withSets([PestLevelSetList::UP_TO_PEST_40]);
```

Of course, if you would rather apply the migration rules for a single version in isolation, the individual `PestSetList::PEST_30` and `PestSetList::PEST_40` sets remain available as well.

---

## Preview & Apply Changes

Run Rector with the `--dry-run` flag to preview changes before applying them:

```bash
vendor/bin/rector process --dry-run
```

Once you are satisfied with the proposed changes, run Rector without the flag to apply them:

```bash
vendor/bin/rector process
```

---

## All Rules

### ChainExpectCallsRector

Chains multiple `expect()` calls on the same value into a single chained expectation

- class: `Pest\Rector\Rules\ChainExpectCallsRector`

```diff
-expect($a)->toBe(10);
-expect($a)->toBeInt();
+expect($a)->toBe(10)
+    ->toBeInt();
```

```diff
-expect($a)->toBe(10);
-expect($b)->toBe(10);
+expect($a)->toBe(10)
+    ->and($b)->toBe(10);
```

### ConvertAssertToExpectRector

Converts PHPUnit assertion method calls to Pest `expect()` chains

- class: `Pest\Rector\Rules\ConvertAssertToExpectRector`

```diff
-$this->assertEquals('expected', $result);
-$this->assertTrue($value);
-$this->assertCount(3, $items);
+expect($result)->toEqual('expected');
+expect($value)->toBeTrue();
+expect($items)->toHaveCount(3);
```

### ConvertBeforeAllInDescribeRector

Replaces invalid `beforeAll()` and `afterAll()` hooks inside `describe()` with `beforeEach()` and `afterEach()`

- class: `Pest\Rector\Rules\ConvertBeforeAllInDescribeRector`

```diff
 describe('users', function (): void {
-    beforeAll(function (): void {
+    beforeEach(function (): void {
         refreshDatabase();
     });
 });
```

### ConvertExpectExceptionToThrowRector

Converts `expectException()` and `expectExceptionMessage()` patterns to `expect()->toThrow()`

- class: `Pest\Rector\Rules\ConvertExpectExceptionToThrowRector`

```diff
-$this->expectException(RuntimeException::class);
-$this->expectExceptionMessage('error');
-doSomething();
+expect(fn () => doSomething())->toThrow(RuntimeException::class, 'error');
```

### EnsureTypeChecksFirstRector

Ensure type-check matchers (e.g. toBeInt, toBeInstanceOf) appear before value assertions in `expect()` chains and consecutive expects

- class: `Pest\Rector\Rules\EnsureTypeChecksFirstRector`

```diff
-expect($a)->toBe(10)->toBeInt();
+expect($a)->toBeInt()->toBe(10);
```

### FixInvalidRepeatValueRector

Normalizes invalid literal `repeat()` counts to 1

- class: `Pest\Rector\Rules\FixInvalidRepeatValueRector`

```diff
 it('retries once', function (): void {
     expect(true)->toBeTrue();
-})->repeat(0);
+})->repeat(1);
```

### RemoveDebugExpectationsRector

Removes debug method calls (dump, dd, ray) from expect chains

- class: `Pest\Rector\Rules\RemoveDebugExpectationsRector`

```diff
-expect($user)->dump()->toBeInstanceOf(User::class);
+expect($user)->toBeInstanceOf(User::class);
```

### RemoveOnlyRector

Removes `only()` from all tests

- class: `Pest\Rector\Rules\RemoveOnlyRector`

```diff
-test()->only();
+test();
```

### RemoveRedundantLiteralTypeExpectationRector

Removes redundant literal type expectations when a later matcher keeps the chain meaningful

- class: `Pest\Rector\Rules\RemoveRedundantLiteralTypeExpectationRector`

```diff
 expect('pest')
-    ->toBeString()
     ->toStartWith('p');
```

### RemoveRedundantPestUsesRector

Removes redundant local Pest `uses` already configured globally in `tests/Pest.php`

- class: `Pest\Rector\Rules\RemoveRedundantPestUsesRector`

```diff
 // tests/Pest.php contains:
 // pest()->use(RefreshDatabase::class)->in('Feature');

 // tests/Feature/UserTest.php
-pest()->use(RefreshDatabase::class, SomeOtherTrait::class);
+pest()->use(SomeOtherTrait::class);
```

### RemoveStaticTestClosureRector

Removes static from Pest test and hook callbacks that use the test case instance

- class: `Pest\Rector\Rules\RemoveStaticTestClosureRector`

```diff
-it('uses the test case instance', static function (): void {
+it('uses the test case instance', function (): void {
     expect($this)->not->toBeNull();
 });
```

### SimplifyComparisonExpectationsRector

Converts `expect($x > 10)->toBeTrue()` to `expect($x)->toBeGreaterThan(10)`

- class: `Pest\Rector\Rules\SimplifyComparisonExpectationsRector`

```diff
-expect($value > 10)->toBeTrue();
-expect($value >= 10)->toBeTrue();
+expect($value)->toBeGreaterThan(10);
+expect($value)->toBeGreaterThanOrEqual(10);
```

### SimplifyExpectNotRector

Simplifies negated expectations by flipping the matcher

- class: `Pest\Rector\Rules\SimplifyExpectNotRector`

```diff
-expect(!$condition)->toBeTrue();
+expect($condition)->toBeFalse();
```

### SimplifyFilesystemMatchersRector

Simplifies combined filesystem checks to single Pest matchers

- class: `Pest\Rector\Rules\SimplifyFilesystemMatchersRector`

```diff
-expect(is_file($path) && is_readable($path))->toBeTrue();
+expect($path)->toBeReadableFile();
```

### SimplifyToBeTruthyFalsyRector

Converts bool cast assertions to `toBeTruthy()/toBeFalsy()` matchers

- class: `Pest\Rector\Rules\SimplifyToBeTruthyFalsyRector`

```diff
-expect((bool) $value)->toBeTrue();
+expect($value)->toBeTruthy();
```

### SimplifyToLiteralBooleanRector

Simplifies `expect($x)->toBe(true)` to `expect($x)->toBeTrue()`

- class: `Pest\Rector\Rules\SimplifyToLiteralBooleanRector`

```diff
-expect($value)->toBe(true);
-expect($value)->toBe(null);
+expect($value)->toBeTrue();
+expect($value)->toBeNull();
```

### TapToDeferRector

Replaces deprecated `->tap()` method with `->defer()` for Pest v3 migration

- class: `Pest\Rector\Rules\Pest2ToPest3\TapToDeferRector`

```diff
-expect($value)->tap(fn ($value) => dump($value))->toBe(10);
+expect($value)->defer(fn ($value) => dump($value))->toBe(10);
```

### ToBeTrueNotFalseRector

Simplifies double-negative expectations like `->not->toBeFalse()` to `->toBeTrue()`

- class: `Pest\Rector\Rules\ToBeTrueNotFalseRector`

```diff
-expect($value)->not->toBeFalse();
+expect($value)->toBeTrue();
```

### ToHaveMethodOnClassRector

Changes `expect($object)->toHaveMethod()` to `expect($object::class)->toHaveMethod()` for Pest v3

- class: `Pest\Rector\Rules\Pest2ToPest3\ToHaveMethodOnClassRector`

```diff
-expect($user)->toHaveMethod('getName');
+expect($user::class)->toHaveMethod('getName');
```

### UseBrowserAriaAndDataAttributeAssertionsRector

Converts `expect($page->attribute($selector, 'aria-*'))->toBe($value)` to `$page->assertAriaAttribute()`, and the `data-*` equivalent (requires the Browser Testing plugin)

- class: `Pest\Rector\Rules\Browser\UseBrowserAriaAndDataAttributeAssertionsRector`

```diff
-expect($page->attribute('button', 'aria-label'))->toBe('Close');
-expect($page->attribute('div', 'data-id'))->toBe('123');
+$page->assertAriaAttribute('button', 'label', 'Close');
+$page->assertDataAttribute('div', 'id', '123');
```

### UseBrowserAttributeAssertionsRector

Converts `expect($page->attribute($selector, $attr))->toBe($value)` to `$page->assertAttribute()` (requires the Browser Testing plugin)

- class: `Pest\Rector\Rules\Browser\UseBrowserAttributeAssertionsRector`

```diff
-expect($page->attribute('img', 'alt'))->toBe('Profile Picture');
+$page->assertAttribute('img', 'alt', 'Profile Picture');
```

### UseBrowserScriptAssertionsRector

Converts `expect($page->script($expression))->toBe($value)` to `$page->assertScript()` (requires the Browser Testing plugin)

- class: `Pest\Rector\Rules\Browser\UseBrowserScriptAssertionsRector`

```diff
-expect($page->script('document.title'))->toBe('Home Page');
+$page->assertScript('document.title', 'Home Page');
```

### UseBrowserSourceAssertionsRector

Converts `expect($page->content())->toContain($html)` to `$page->assertSourceHas()` (requires the Browser Testing plugin)

- class: `Pest\Rector\Rules\Browser\UseBrowserSourceAssertionsRector`

```diff
-expect($page->content())->toContain('<h1>Welcome</h1>');
+$page->assertSourceHas('<h1>Welcome</h1>');
```

### UseBrowserUrlAssertionsRector

Converts `expect($page->url())->toBe($url)` to `$page->assertUrlIs()` (requires the Browser Testing plugin)

- class: `Pest\Rector\Rules\Browser\UseBrowserUrlAssertionsRector`

```diff
-expect($page->url())->toBe('https://example.com/home');
+$page->assertUrlIs('https://example.com/home');
```

### UseBrowserValueAssertionsRector

Converts `expect($page->value($selector))->toBe($value)` to `$page->assertValue()` (requires the Browser Testing plugin)

- class: `Pest\Rector\Rules\Browser\UseBrowserValueAssertionsRector`

```diff
-expect($page->value('input[name=email]'))->toBe('test@example.com');
+$page->assertValue('input[name=email]', 'test@example.com');
```

### UseEachModifierRector

Converts foreach loops with `expect()` calls to use the `->each` modifier

- class: `Pest\Rector\Rules\UseEachModifierRector`

```diff
-foreach ($items as $item) {
-    expect($item)->toBeString();
-}
+expect($items)->each->toBeString();
```

### UseInstanceOfMatcherRector

Converts `expect($obj instanceof User)->toBeTrue()` to `expect($obj)->toBeInstanceOf(User::class)`

- class: `Pest\Rector\Rules\UseInstanceOfMatcherRector`

```diff
-expect($user instanceof User)->toBeTrue();
+expect($user)->toBeInstanceOf(User::class);
```

### UseSequenceMatcherRector

Converts consecutive indexed `expect()` calls to `sequence()`

- class: `Pest\Rector\Rules\UseSequenceMatcherRector`

```diff
-expect($items[0])->toBe('a');
-expect($items[1])->toBe('b');
+expect($items)->sequence(fn ($e) => $e->toBe('a'), fn ($e) => $e->toBe('b'));
```

### UseStrictEqualityMatchersRector

Converts strict equality expressions to `toBe()` matcher

- class: `Pest\Rector\Rules\UseStrictEqualityMatchersRector`

```diff
-expect($a === $b)->toBeTrue();
+expect($a)->toBe($b);
```

### UseToBeAlphaNumericRector

Converts `ctype_alnum()` checks to `toBeAlphaNumeric()` matcher

- class: `Pest\Rector\Rules\UseToBeAlphaNumericRector`

```diff
-expect(ctype_alnum($value))->toBeTrue();
+expect($value)->toBeAlphaNumeric();
```

### UseToBeAlphaRector

Converts `ctype_alpha()` checks to `toBeAlpha()` matcher

- class: `Pest\Rector\Rules\UseToBeAlphaRector`

```diff
-expect(ctype_alpha($value))->toBeTrue();
+expect($value)->toBeAlpha();
```

### UseToBeBetweenRector

Converts `expect($value >= $min && $value <= $max)->toBeTrue()` to `expect($value)->toBeBetween($min, $max)`

- class: `Pest\Rector\Rules\UseToBeBetweenRector`

```diff
-expect($value >= 1 && $value <= 10)->toBeTrue();
+expect($value)->toBeBetween(1, 10);
```

### UseToBeCamelCaseRector

Converts `Str::camel()` equality checks to `toBeCamelCase()` matcher (requires `illuminate/support`)

- class: `Pest\Rector\Rules\UseToBeCamelCaseRector`

```diff
-expect(Str::camel($value) === $value)->toBeTrue();
+expect($value)->toBeCamelCase();
```

### UseToBeDigitsRector

Converts `ctype_digit()` checks to `toBeDigits()` matcher

- class: `Pest\Rector\Rules\UseToBeDigitsRector`

```diff
-expect(ctype_digit($value))->toBeTrue();
+expect($value)->toBeDigits();
```

### UseToBeDirectoryRector

Converts `is_dir()` checks to `toBeDirectory()` matcher

- class: `Pest\Rector\Rules\UseToBeDirectoryRector`

```diff
-expect(is_dir($path))->toBeTrue();
+expect($path)->toBeDirectory();
```

### UseToBeEmptyRector

Converts empty checks and count-zero comparisons to `toBeEmpty()` matcher

- class: `Pest\Rector\Rules\UseToBeEmptyRector`

```diff
-expect(empty($value))->toBeTrue();
+expect($value)->toBeEmpty();
```

### UseToBeFileRector

Converts `is_file()` checks to `toBeFile()` matcher

- class: `Pest\Rector\Rules\UseToBeFileRector`

```diff
-expect(is_file($path))->toBeTrue();
+expect($path)->toBeFile();
```

### UseToBeInRector

Converts strict `in_array()` checks to the `toBeIn()` matcher

- class: `Pest\Rector\Rules\UseToBeInRector`

```diff
-expect(in_array($value, ['pending', 'active'], true))->toBeTrue();
+expect($value)->toBeIn(['pending', 'active']);
```

### UseToBeInfiniteRector

Converts `is_infinite()` checks to `toBeInfinite()` matcher

- class: `Pest\Rector\Rules\UseToBeInfiniteRector`

```diff
-expect(is_infinite($value))->toBeTrue();
+expect($value)->toBeInfinite();
```

### UseToBeJsonRector

Converts `json_decode()` null checks to `toBeJson()` matcher

- class: `Pest\Rector\Rules\UseToBeJsonRector`

```diff
-expect(json_decode($string) !== null)->toBeTrue();
+expect($string)->toBeJson();
```

### UseToBeKebabCaseRector

Converts `Str::kebab()` equality checks to `toBeKebabCase()` matcher (requires `illuminate/support`)

- class: `Pest\Rector\Rules\UseToBeKebabCaseRector`

```diff
-expect(Str::kebab($value) === $value)->toBeTrue();
+expect($value)->toBeKebabCase();
```

### UseToBeListRector

Converts `array_is_list()` checks to `toBeList()` matcher

- class: `Pest\Rector\Rules\UseToBeListRector`

```diff
-expect(array_is_list($array))->toBeTrue();
+expect($array)->toBeList();
```

### UseToBeLowercaseRector

Converts `strtolower()` equality checks to `toBeLowercase()` matcher

- class: `Pest\Rector\Rules\UseToBeLowercaseRector`

```diff
-expect(strtolower($value) === $value)->toBeTrue();
+expect($value)->toBeLowercase();
```

### UseToBeNanRector

Converts `is_nan()` checks to `toBeNan()` matcher

- class: `Pest\Rector\Rules\UseToBeNanRector`

```diff
-expect(is_nan($value))->toBeTrue();
+expect($value)->toBeNan();
```

### UseToBeSlugRector

Converts `Str::slug()` equality checks to `toBeSlug()` matcher (requires `illuminate/support`)

- class: `Pest\Rector\Rules\UseToBeSlugRector`

```diff
-expect(Str::slug($value) === $value)->toBeTrue();
+expect($value)->toBeSlug();
```

### UseToBeSnakeCaseRector

Converts `Str::snake()` equality checks to `toBeSnakeCase()` matcher (requires `illuminate/support`)

- class: `Pest\Rector\Rules\UseToBeSnakeCaseRector`

```diff
-expect(Str::snake($value) === $value)->toBeTrue();
+expect($value)->toBeSnakeCase();
```

### UseToBeStudlyCaseRector

Converts `Str::studly()` equality checks to `toBeStudlyCase()` matcher (requires `illuminate/support`)

- class: `Pest\Rector\Rules\UseToBeStudlyCaseRector`

```diff
-expect(Str::studly($value) === $value)->toBeTrue();
+expect($value)->toBeStudlyCase();
```

### UseToBeUppercaseRector

Converts `strtoupper()` equality checks to `toBeUppercase()` matcher

- class: `Pest\Rector\Rules\UseToBeUppercaseRector`

```diff
-expect(strtoupper($value) === $value)->toBeTrue();
+expect($value)->toBeUppercase();
```

### UseToBeUrlRector

Converts `filter_var($url, FILTER_VALIDATE_URL)` checks to `toBeUrl()` matcher

- class: `Pest\Rector\Rules\UseToBeUrlRector`

```diff
-expect(filter_var($url, FILTER_VALIDATE_URL))->not->toBeFalse();
+expect($url)->toBeUrl();
```

### UseToBeUuidRector

Converts UUID regex validation to `toBeUuid()` matcher

- class: `Pest\Rector\Rules\UseToBeUuidRector`

```diff
-expect(preg_match('/^[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}$/i', $value))->toBe(1);
+expect($value)->toBeUuid();
```

### UseToContainEqualRector

Converts `in_array(..., false)` checks to `toContainEqual()` matcher

- class: `Pest\Rector\Rules\UseToContainEqualRector`

```diff
-expect(in_array($item, $array, false))->toBeTrue();
+expect($array)->toContainEqual($item);
```

### UseToContainOnlyInstancesOfRector

Converts `->each->toBeInstanceOf()` pattern to `toContainOnlyInstancesOf()` matcher

- class: `Pest\Rector\Rules\UseToContainOnlyInstancesOfRector`

```diff
-expect($items)->each->toBeInstanceOf(User::class);
+expect($items)->toContainOnlyInstancesOf(User::class);
```

### UseToContainRector

Converts `in_array()` checks to `toContain()` matcher

- class: `Pest\Rector\Rules\UseToContainRector`

```diff
-expect(in_array($item, $array))->toBeTrue();
+expect($array)->toContain($item);
```

### UseToEndWithRector

Converts `str_ends_with()` checks to `toEndWith()` matcher

- class: `Pest\Rector\Rules\UseToEndWithRector`

```diff
-expect(str_ends_with($string, 'World'))->toBeTrue();
+expect($string)->toEndWith('World');
```

### UseToEqualCanonicalizingRector

Converts sort-then-compare to `toEqualCanonicalizing()` matcher

- class: `Pest\Rector\Rules\UseToEqualCanonicalizingRector`

```diff
-expect(sort($a))->toEqual(sort($b));
+expect($a)->toEqualCanonicalizing($b);
```

### UseToEqualWithDeltaRector

Converts `expect(abs($a - $b) < $delta)->toBeTrue()` to `expect($a)->toEqualWithDelta($b, $delta)`

- class: `Pest\Rector\Rules\UseToEqualWithDeltaRector`

```diff
-expect(abs($a - $b) < 0.001)->toBeTrue();
+expect($a)->toEqualWithDelta($b, 0.001);
```

### UseToHaveCountRector

Converts `expect(count($arr))->toBe(5)` to `expect($arr)->toHaveCount(5)`

- class: `Pest\Rector\Rules\UseToHaveCountRector`

```diff
-expect(count($array))->toBe(5);
+expect($array)->toHaveCount(5);
```

### UseToHaveKeyRector

Converts `array_key_exists()` checks to `toHaveKey()` matcher

- class: `Pest\Rector\Rules\UseToHaveKeyRector`

```diff
-expect(array_key_exists('id', $array))->toBeTrue();
+expect($array)->toHaveKey('id');
```

### UseToHaveKeysRector

Converts chained `toHaveKey()` calls to `toHaveKeys()` with array of keys

- class: `Pest\Rector\Rules\UseToHaveKeysRector`

```diff
-expect($array)->toHaveKey('id')->toHaveKey('name');
+expect($array)->toHaveKeys(['id', 'name']);
```

### UseToHaveLengthRector

Converts `strlen()/mb_strlen()` comparisons to `toHaveLength()` matcher

- class: `Pest\Rector\Rules\UseToHaveLengthRector`

```diff
-expect(strlen($string))->toBe(10);
+expect($string)->toHaveLength(10);
```

### UseToHavePropertiesRector

Converts chained `toHaveProperty()` calls to `toHaveProperties()` with array of properties

- class: `Pest\Rector\Rules\UseToHavePropertiesRector`

```diff
-expect($user)->toHaveProperty('name')->toHaveProperty('email');
+expect($user)->toHaveProperties(['name', 'email']);
```

### UseToHavePropertyRector

Converts `property_exists()` checks to `toHaveProperty()` matcher

- class: `Pest\Rector\Rules\UseToHavePropertyRector`

```diff
-expect(property_exists($object, 'name'))->toBeTrue();
+expect($object)->toHaveProperty('name');
```

### UseToHaveSameSizeRector

Converts `expect(count($a))->toBe(count($b))` to `expect($a)->toHaveSameSize($b)`

- class: `Pest\Rector\Rules\UseToHaveSameSizeRector`

```diff
-expect(count($array1))->toBe(count($array2));
+expect($array1)->toHaveSameSize($array2);
```

### UseToMatchArrayRector

Converts multiple array element assertions to `toMatchArray()` matcher

- class: `Pest\Rector\Rules\UseToMatchArrayRector`

```diff
-expect($array['name'])->toBe('Nuno');
-expect($array['email'])->toBe('nuno@example.com');
+expect($array)->toMatchArray(['name' => 'Nuno', 'email' => 'nuno@example.com']);
```

### UseToMatchObjectRector

Converts consecutive `toHaveProperty()` with values to `toMatchObject()` matcher

- class: `Pest\Rector\Rules\UseToMatchObjectRector`

```diff
-expect($user)->toHaveProperty('name', 'Nuno');
-expect($user)->toHaveProperty('email', 'nuno@example.com');
+expect($user)->toMatchObject(['name' => 'Nuno', 'email' => 'nuno@example.com']);
```

### UseToMatchRector

Converts `expect(preg_match("/pattern/", $str))->toBe(1)` to `expect($str)->toMatch("/pattern/")`

- class: `Pest\Rector\Rules\UseToMatchRector`

```diff
-expect(preg_match('/pattern/', $string))->toBe(1);
+expect($string)->toMatch('/pattern/');
```

### UseToStartWithRector

Converts `str_starts_with()` checks to `toStartWith()` matcher

- class: `Pest\Rector\Rules\UseToStartWithRector`

```diff
-expect(str_starts_with($string, 'Hello'))->toBeTrue();
+expect($string)->toStartWith('Hello');
```

### UseToThrowRector

Converts `try`/`catch` patterns in Pest tests to `expect()->toThrow()`

- class: `Pest\Rector\Rules\UseToThrowRector`

```diff
 test('it throws an error', function () {
-    try {
-        doSomething();
-    } catch (RuntimeException $e) {
-        expect($e->getMessage())->toBe('error');
-    }
+    expect(fn () => doSomething())->toThrow(RuntimeException::class, 'error');
 });
```

### UseTypeMatchersRector

Converts `expect(is_array($x))->toBeTrue()` to `expect($x)->toBeArray()`

- class: `Pest\Rector\Rules\UseTypeMatchersRector`

```diff
-expect(is_array($value))->toBeTrue();
+expect($value)->toBeArray();
```

### UsesToExtendRector

Converts `uses()` and `pest()->uses()` to `pest()->extend()` for classes and `pest()->use()` for traits

- class: `Pest\Rector\Rules\Pest2ToPest3\UsesToExtendRector`

```diff
-uses(Tests\TestCase::class)->in('Feature');
+pest()->extend(Tests\TestCase::class)->in('Feature');
```

Now that you know how to automate refactoring your test suite, let's look at how Pest's PHPStan plugin brings accurate static analysis to your tests: [PHPStan](/docs/phpstan)