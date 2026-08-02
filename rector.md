---
title: Rector
description: Pest's Rector plugin provides automated refactoring rules powered by Rector to modernize your test code and upgrade between Pest versions.
---

# Rector

**Source code**: [github.com/pestphp/pest-plugin-rector](https://github.com/pestphp/pest-plugin-rector)

Pest's Rector plugin provides automated refactoring rules powered by [Rector](https://getrector.org/). It helps you simplify and modernize your test code, as well as upgrade between major Pest versions.

<iframe width="100%" height="315" src="https://www.youtube.com/embed/oNU41o3pz5M" title="rector rewrote 4000 tests while i got coffee (pest 5, day 5/6)" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

To get started, require the plugin via Composer and install Rector:

```bash
composer require pestphp/pest-plugin-rector --dev
composer require rector/rector --dev
```

## Rule Sets

The plugin provides a predefined rule set: `PestSetList::CODING_STYLE`. You may register this set in your project's `rector.php` file.

The `CODING_STYLE` set rewrites raw PHP assertions into Pest's expressive, built-in matchers, and simplifies redundant expectation patterns. For example, it converts `expect(count($array))->toBe(3)` into `expect($array)->toHaveCount(3)`.

It also merges consecutive expectations on the same value into a single, fluent chain, and orders type checks first within each chain, so that your assertions read from the most general to the most specific. In addition, it includes rules to upgrade your test suite between major Pest versions:

```php
use Pest\Rector\Set\PestSetList;
use Rector\Config\RectorConfig;

return RectorConfig::configure()
    ->withPaths([__DIR__ . '/tests'])
    ->withSets([
        PestSetList::CODING_STYLE,
    ]);
```

## Preview & Apply Changes

To preview changes before applying them, you may run Rector with the `--dry-run` flag:

```bash
vendor/bin/rector process --dry-run
```

Once you are satisfied with the proposed changes, you may run Rector without the flag to apply them:

```bash
vendor/bin/rector process
```

## All Rules

For reference, below is every rule provided by the plugin, along with an example of the transformation it applies.

### ChainExpectCallsRector

- class: `Pest\Rector\Rules\ChainExpectCallsRector`

This rule chains multiple `expect()` calls on the same value into a single chained expectation:

```diff
-expect($a)->toBe(10);
-expect($a)->toBeInt();
+expect($a)->toBe(10)
+    ->toBeInt();
```

By default, the rule also joins expectations on different values with `->and()`:

```diff
-expect($a)->toBe(10);
-expect($b)->toBe(10);
+expect($a)->toBe(10)
+    ->and($b)->toBe(10);
```

#### Configuration

Sometimes, you may wish to only merge expectations on the same value, leaving expectations on different values untouched. To accomplish this, you may set the `merge_different_variables` option to `false` in your project's `rector.php` file:

```php
use Pest\Rector\Rules\ChainExpectCallsRector;
use Rector\Config\RectorConfig;

return RectorConfig::configure()
    ->withPaths([__DIR__ . '/tests'])
    ->withConfiguredRule(ChainExpectCallsRector::class, [
        'merge_different_variables' => false,
    ]);
```

The option defaults to `true`, so expectations on different values will be joined with `->and()` unless you opt out. For example, with `merge_different_variables` set to `false`, only expectations on the same value will be merged:

```diff
-expect($a)->toBe(10);
-expect($a)->toBeInt();
-expect($b)->toBe(10);
+expect($a)->toBe(10)
+    ->toBeInt();
+expect($b)->toBe(10);
```

### ConvertAssertToExpectRector

- class: `Pest\Rector\Rules\ConvertAssertToExpectRector`

This rule converts `$this->assert*()` calls to Pest `expect()` chains:

```diff
-$this->assertEquals('expected', $result);
-$this->assertTrue($value);
-$this->assertCount(3, $items);
+expect($result)->toEqual('expected');
+expect($value)->toBeTrue();
+expect($items)->toHaveCount(3);
```

### ConvertBeforeAllInDescribeRector

- class: `Pest\Rector\Rules\ConvertBeforeAllInDescribeRector`

This rule replaces invalid `beforeAll()` and `afterAll()` hooks inside `describe()` with `beforeEach()` and `afterEach()`:

```diff
 describe('users', function (): void {
-    beforeAll(function (): void {
+    beforeEach(function (): void {
         refreshDatabase();
     });
 });
```

### ConvertExpectExceptionToThrowRector

- class: `Pest\Rector\Rules\ConvertExpectExceptionToThrowRector`

This rule converts `$this->expectException()` and `$this->expectExceptionMessage()` patterns to `expect()->toThrow()`:

```diff
-$this->expectException(RuntimeException::class);
-$this->expectExceptionMessage('error');
-doSomething();
+expect(fn () => doSomething())->toThrow(RuntimeException::class, 'error');
```

### EnsureTypeChecksFirstRector

- class: `Pest\Rector\Rules\EnsureTypeChecksFirstRector`

This rule ensures that type-check matchers, such as `toBeInt()` and `toBeInstanceOf()`, appear before value assertions in `expect()` chains and consecutive expectations:

```diff
-expect($a)->toBe(10)->toBeInt();
+expect($a)->toBeInt()->toBe(10);
```

### FixInvalidRepeatValueRector

- class: `Pest\Rector\Rules\FixInvalidRepeatValueRector`

This rule normalizes invalid literal `repeat()` counts to `1`:

```diff
 it('retries once', function (): void {
     expect(true)->toBeTrue();
-})->repeat(0);
+})->repeat(1);
```

### RemoveDebugExpectationsRector

- class: `Pest\Rector\Rules\RemoveDebugExpectationsRector`

This rule removes debug method calls, such as `dump()`, `dd()`, and `ray()`, from `expect()` chains:

```diff
-expect($user)->dump()->toBeInstanceOf(User::class);
+expect($user)->toBeInstanceOf(User::class);
```

### RemoveOnlyRector

- class: `Pest\Rector\Rules\RemoveOnlyRector`

This rule removes `only()` from all tests:

```diff
-test()->only();
+test();
```

### RemoveRedundantLiteralTypeExpectationRector

- class: `Pest\Rector\Rules\RemoveRedundantLiteralTypeExpectationRector`

This rule removes redundant literal type expectations when a later matcher keeps the chain meaningful:

```diff
 expect('pest')
-    ->toBeString()
     ->toStartWith('p');
```

### RemoveRedundantPestUsesRector

- class: `Pest\Rector\Rules\RemoveRedundantPestUsesRector`

This rule removes redundant local Pest `uses()` calls that are already configured globally in `tests/Pest.php`:

```diff
 // tests/Pest.php contains:
 // pest()->use(RefreshDatabase::class)->in('Feature');

 // tests/Feature/UserTest.php
-pest()->use(RefreshDatabase::class, SomeOtherTrait::class);
+pest()->use(SomeOtherTrait::class);
```

### RemoveStaticTestClosureRector

- class: `Pest\Rector\Rules\RemoveStaticTestClosureRector`

This rule removes the `static` keyword from Pest test and hook callbacks that use the test case instance:

```diff
-it('uses the test case instance', static function (): void {
+it('uses the test case instance', function (): void {
     expect($this)->not->toBeNull();
 });
```

### SimplifyComparisonExpectationsRector

- class: `Pest\Rector\Rules\SimplifyComparisonExpectationsRector`

This rule converts `expect($x > 10)->toBeTrue()` to `expect($x)->toBeGreaterThan(10)`:

```diff
-expect($value > 10)->toBeTrue();
-expect($value >= 10)->toBeTrue();
+expect($value)->toBeGreaterThan(10);
+expect($value)->toBeGreaterThanOrEqual(10);
```

### SimplifyExpectNotRector

- class: `Pest\Rector\Rules\SimplifyExpectNotRector`

This rule simplifies negated expectations by flipping the matcher:

```diff
-expect(!$condition)->toBeTrue();
+expect($condition)->toBeFalse();
```

### SimplifyFilesystemMatchersRector

- class: `Pest\Rector\Rules\SimplifyFilesystemMatchersRector`

This rule simplifies combined filesystem checks to single Pest matchers:

```diff
-expect(is_file($path) && is_readable($path))->toBeTrue();
+expect($path)->toBeReadableFile();
```

### SimplifyToBeTruthyFalsyRector

- class: `Pest\Rector\Rules\SimplifyToBeTruthyFalsyRector`

This rule converts boolean cast assertions to the `toBeTruthy()/toBeFalsy()` matchers:

```diff
-expect((bool) $value)->toBeTrue();
+expect($value)->toBeTruthy();
```

### SimplifyToLiteralBooleanRector

- class: `Pest\Rector\Rules\SimplifyToLiteralBooleanRector`

This rule simplifies `expect($x)->toBe(true)` to `expect($x)->toBeTrue()`:

```diff
-expect($value)->toBe(true);
-expect($value)->toBe(null);
+expect($value)->toBeTrue();
+expect($value)->toBeNull();
```

### TapToDeferRector

- class: `Pest\Rector\Rules\Pest2ToPest3\TapToDeferRector`

This rule replaces the deprecated `->tap()` method with `->defer()` for the Pest v3 migration:

```diff
-expect($value)->tap(fn ($value) => dump($value))->toBe(10);
+expect($value)->defer(fn ($value) => dump($value))->toBe(10);
```

### ToBeTrueNotFalseRector

- class: `Pest\Rector\Rules\ToBeTrueNotFalseRector`

This rule simplifies double-negative expectations like `->not->toBeFalse()` to `->toBeTrue()`:

```diff
-expect($value)->not->toBeFalse();
+expect($value)->toBeTrue();
```

### ToHaveMethodOnClassRector

- class: `Pest\Rector\Rules\Pest2ToPest3\ToHaveMethodOnClassRector`

This rule changes `expect($object)->toHaveMethod()` to `expect($object::class)->toHaveMethod()` for Pest v3:

```diff
-expect($user)->toHaveMethod('getName');
+expect($user::class)->toHaveMethod('getName');
```

### UseEachModifierRector

- class: `Pest\Rector\Rules\UseEachModifierRector`

This rule converts `foreach` loops with `expect()` calls to use the `->each` modifier:

```diff
-foreach ($items as $item) {
-    expect($item)->toBeString();
-}
+expect($items)->each->toBeString();
```

### UseInstanceOfMatcherRector

- class: `Pest\Rector\Rules\UseInstanceOfMatcherRector`

This rule converts `expect($obj instanceof User)->toBeTrue()` to `expect($obj)->toBeInstanceOf(User::class)`:

```diff
-expect($user instanceof User)->toBeTrue();
+expect($user)->toBeInstanceOf(User::class);
```

### UseSequenceMatcherRector

- class: `Pest\Rector\Rules\UseSequenceMatcherRector`

This rule converts consecutive indexed `expect()` calls to `sequence()`:

```diff
-expect($items[0])->toBe('a');
-expect($items[1])->toBe('b');
+expect($items)->sequence(fn ($e) => $e->toBe('a'), fn ($e) => $e->toBe('b'));
```

### UseStrictEqualityMatchersRector

- class: `Pest\Rector\Rules\UseStrictEqualityMatchersRector`

This rule converts strict equality expressions to the `toBe()` matcher:

```diff
-expect($a === $b)->toBeTrue();
+expect($a)->toBe($b);
```

### UseToBeAlphaNumericRector

- class: `Pest\Rector\Rules\UseToBeAlphaNumericRector`

This rule converts `ctype_alnum()` checks to the `toBeAlphaNumeric()` matcher:

```diff
-expect(ctype_alnum($value))->toBeTrue();
+expect($value)->toBeAlphaNumeric();
```

### UseToBeAlphaRector

- class: `Pest\Rector\Rules\UseToBeAlphaRector`

This rule converts `ctype_alpha()` checks to the `toBeAlpha()` matcher:

```diff
-expect(ctype_alpha($value))->toBeTrue();
+expect($value)->toBeAlpha();
```

### UseToBeBetweenRector

- class: `Pest\Rector\Rules\UseToBeBetweenRector`

This rule converts `expect($value >= $min && $value <= $max)->toBeTrue()` to `expect($value)->toBeBetween($min, $max)`:

```diff
-expect($value >= 1 && $value <= 10)->toBeTrue();
+expect($value)->toBeBetween(1, 10);
```

### UseToBeDigitsRector

- class: `Pest\Rector\Rules\UseToBeDigitsRector`

This rule converts `ctype_digit()` checks to the `toBeDigits()` matcher:

```diff
-expect(ctype_digit($value))->toBeTrue();
+expect($value)->toBeDigits();
```

### UseToBeDirectoryRector

- class: `Pest\Rector\Rules\UseToBeDirectoryRector`

This rule converts `is_dir()` checks to the `toBeDirectory()` matcher:

```diff
-expect(is_dir($path))->toBeTrue();
+expect($path)->toBeDirectory();
```

### UseToBeEmptyRector

- class: `Pest\Rector\Rules\UseToBeEmptyRector`

This rule converts empty checks and count-zero comparisons to the `toBeEmpty()` matcher:

```diff
-expect(empty($value))->toBeTrue();
+expect($value)->toBeEmpty();
```

### UseToBeFileRector

- class: `Pest\Rector\Rules\UseToBeFileRector`

This rule converts `is_file()` checks to the `toBeFile()` matcher:

```diff
-expect(is_file($path))->toBeTrue();
+expect($path)->toBeFile();
```

### UseToBeInRector

- class: `Pest\Rector\Rules\UseToBeInRector`

This rule converts strict `in_array()` checks to the `toBeIn()` matcher:

```diff
-expect(in_array($value, ['pending', 'active'], true))->toBeTrue();
+expect($value)->toBeIn(['pending', 'active']);
```

### UseToBeInfiniteRector

- class: `Pest\Rector\Rules\UseToBeInfiniteRector`

This rule converts `is_infinite()` checks to the `toBeInfinite()` matcher:

```diff
-expect(is_infinite($value))->toBeTrue();
+expect($value)->toBeInfinite();
```

### UseToBeJsonRector

- class: `Pest\Rector\Rules\UseToBeJsonRector`

This rule converts `json_decode()` null checks to the `toBeJson()` matcher:

```diff
-expect(json_decode($string) !== null)->toBeTrue();
+expect($string)->toBeJson();
```

### UseToBeListRector

- class: `Pest\Rector\Rules\UseToBeListRector`

This rule converts `array_is_list()` checks to the `toBeList()` matcher:

```diff
-expect(array_is_list($array))->toBeTrue();
+expect($array)->toBeList();
```

### UseToBeLowercaseRector

- class: `Pest\Rector\Rules\UseToBeLowercaseRector`

This rule converts `strtolower()` equality checks to the `toBeLowercase()` matcher:

```diff
-expect(strtolower($value) === $value)->toBeTrue();
+expect($value)->toBeLowercase();
```

### UseToBeNanRector

- class: `Pest\Rector\Rules\UseToBeNanRector`

This rule converts `is_nan()` checks to the `toBeNan()` matcher:

```diff
-expect(is_nan($value))->toBeTrue();
+expect($value)->toBeNan();
```

### UseToBeUppercaseRector

- class: `Pest\Rector\Rules\UseToBeUppercaseRector`

This rule converts `strtoupper()` equality checks to the `toBeUppercase()` matcher:

```diff
-expect(strtoupper($value) === $value)->toBeTrue();
+expect($value)->toBeUppercase();
```

### UseToBeUrlRector

- class: `Pest\Rector\Rules\UseToBeUrlRector`

This rule converts `filter_var($url, FILTER_VALIDATE_URL)` checks to the `toBeUrl()` matcher:

```diff
-expect(filter_var($url, FILTER_VALIDATE_URL))->not->toBeFalse();
+expect($url)->toBeUrl();
```

### UseToBeUuidRector

- class: `Pest\Rector\Rules\UseToBeUuidRector`

This rule converts UUID regex validation to the `toBeUuid()` matcher:

```diff
-expect(preg_match('/^[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}$/i', $value))->toBe(1);
+expect($value)->toBeUuid();
```

### UseToContainEqualRector

- class: `Pest\Rector\Rules\UseToContainEqualRector`

This rule converts `in_array(..., false)` checks to the `toContainEqual()` matcher:

```diff
-expect(in_array($item, $array, false))->toBeTrue();
+expect($array)->toContainEqual($item);
```

### UseToContainOnlyInstancesOfRector

- class: `Pest\Rector\Rules\UseToContainOnlyInstancesOfRector`

This rule converts the `->each->toBeInstanceOf()` pattern to the `toContainOnlyInstancesOf()` matcher:

```diff
-expect($items)->each->toBeInstanceOf(User::class);
+expect($items)->toContainOnlyInstancesOf(User::class);
```

### UseToContainRector

- class: `Pest\Rector\Rules\UseToContainRector`

This rule converts `in_array()` checks to the `toContain()` matcher:

```diff
-expect(in_array($item, $array))->toBeTrue();
+expect($array)->toContain($item);
```

### UseToEndWithRector

- class: `Pest\Rector\Rules\UseToEndWithRector`

This rule converts `str_ends_with()` checks to the `toEndWith()` matcher:

```diff
-expect(str_ends_with($string, 'World'))->toBeTrue();
+expect($string)->toEndWith('World');
```

### UseToEqualCanonicalizingRector

- class: `Pest\Rector\Rules\UseToEqualCanonicalizingRector`

This rule converts sort-then-compare patterns to the `toEqualCanonicalizing()` matcher:

```diff
-expect(sort($a))->toEqual(sort($b));
+expect($a)->toEqualCanonicalizing($b);
```

### UseToEqualWithDeltaRector

- class: `Pest\Rector\Rules\UseToEqualWithDeltaRector`

This rule converts `expect(abs($a - $b) < $delta)->toBeTrue()` to `expect($a)->toEqualWithDelta($b, $delta)`:

```diff
-expect(abs($a - $b) < 0.001)->toBeTrue();
+expect($a)->toEqualWithDelta($b, 0.001);
```

### UseToHaveCountRector

- class: `Pest\Rector\Rules\UseToHaveCountRector`

This rule converts `expect(count($arr))->toBe(5)` to `expect($arr)->toHaveCount(5)`:

```diff
-expect(count($array))->toBe(5);
+expect($array)->toHaveCount(5);
```

### UseToHaveKeyRector

- class: `Pest\Rector\Rules\UseToHaveKeyRector`

This rule converts `array_key_exists()` checks to the `toHaveKey()` matcher:

```diff
-expect(array_key_exists('id', $array))->toBeTrue();
+expect($array)->toHaveKey('id');
```

### UseToHaveKeysRector

- class: `Pest\Rector\Rules\UseToHaveKeysRector`

This rule converts chained `toHaveKey()` calls to `toHaveKeys()` with an array of keys:

```diff
-expect($array)->toHaveKey('id')->toHaveKey('name');
+expect($array)->toHaveKeys(['id', 'name']);
```

### UseToHaveLengthRector

- class: `Pest\Rector\Rules\UseToHaveLengthRector`

This rule converts `strlen()/mb_strlen()` comparisons to the `toHaveLength()` matcher:

```diff
-expect(strlen($string))->toBe(10);
+expect($string)->toHaveLength(10);
```

### UseToHavePropertiesRector

- class: `Pest\Rector\Rules\UseToHavePropertiesRector`

This rule converts chained `toHaveProperty()` calls to `toHaveProperties()` with an array of properties:

```diff
-expect($user)->toHaveProperty('name')->toHaveProperty('email');
+expect($user)->toHaveProperties(['name', 'email']);
```

### UseToHavePropertyRector

- class: `Pest\Rector\Rules\UseToHavePropertyRector`

This rule converts `property_exists()` checks to the `toHaveProperty()` matcher:

```diff
-expect(property_exists($object, 'name'))->toBeTrue();
+expect($object)->toHaveProperty('name');
```

### UseToHaveSameSizeRector

- class: `Pest\Rector\Rules\UseToHaveSameSizeRector`

This rule converts `expect(count($a))->toBe(count($b))` to `expect($a)->toHaveSameSize($b)`:

```diff
-expect(count($array1))->toBe(count($array2));
+expect($array1)->toHaveSameSize($array2);
```

### UseToMatchArrayRector

- class: `Pest\Rector\Rules\UseToMatchArrayRector`

This rule converts multiple array element assertions to the `toMatchArray()` matcher:

```diff
-expect($array['name'])->toBe('Nuno');
-expect($array['email'])->toBe('nuno@example.com');
+expect($array)->toMatchArray(['name' => 'Nuno', 'email' => 'nuno@example.com']);
```

### UseToMatchObjectRector

- class: `Pest\Rector\Rules\UseToMatchObjectRector`

This rule converts consecutive `toHaveProperty()` calls with values to the `toMatchObject()` matcher:

```diff
-expect($user)->toHaveProperty('name', 'Nuno');
-expect($user)->toHaveProperty('email', 'nuno@example.com');
+expect($user)->toMatchObject(['name' => 'Nuno', 'email' => 'nuno@example.com']);
```

### UseToMatchRector

- class: `Pest\Rector\Rules\UseToMatchRector`

This rule converts `expect(preg_match("/pattern/", $str))->toBe(1)` to `expect($str)->toMatch("/pattern/")`:

```diff
-expect(preg_match('/pattern/', $string))->toBe(1);
+expect($string)->toMatch('/pattern/');
```

### UseToStartWithRector

- class: `Pest\Rector\Rules\UseToStartWithRector`

This rule converts `str_starts_with()` checks to the `toStartWith()` matcher:

```diff
-expect(str_starts_with($string, 'Hello'))->toBeTrue();
+expect($string)->toStartWith('Hello');
```

### UseToThrowRector

- class: `Pest\Rector\Rules\UseToThrowRector`

This rule converts `try`/`catch` patterns in Pest tests to `expect()->toThrow()`:

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

- class: `Pest\Rector\Rules\UseTypeMatchersRector`

This rule converts `expect(is_array($x))->toBeTrue()` to `expect($x)->toBeArray()`:

```diff
-expect(is_array($value))->toBeTrue();
+expect($value)->toBeArray();
```

### UsesToExtendRector

- class: `Pest\Rector\Rules\Pest2ToPest3\UsesToExtendRector`

This rule converts `uses()` and `pest()->uses()` to `pest()->extend()` for classes and `pest()->use()` for traits:

```diff
-uses(Tests\TestCase::class)->in('Feature');
+pest()->extend(Tests\TestCase::class)->in('Feature');
```

---

Now that you know how to automate refactoring your test suite, let's look at how Pest's PHPStan plugin brings accurate static analysis to your tests: [PHPStan →](/docs/phpstan)