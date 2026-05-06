---
title: Browser Testing
description:
---

# Browser Testing

Browser testing is an essential part of modern web development, allowing you to ensure that your application works correctly across different browsers and devices. Pest provides a simple and elegant way to write browser tests. Here is an example of how to write a browser test using Pest:

```php
it('may welcome the user', function () {
    $page = visit('/');

    $page->assertSee('Welcome');
});
```

This is a basic example of a browser test that checks if the homepage contains the text "Welcome". However, Pest's browser testing capabilities go beyond this simple example. You can use various methods to interact with the page, such as clicking buttons, filling out forms, and navigating between pages.

Here is an example of a more complex browser test, on Laravel, that checks if a user can sign in:

```php
it('may sign in the user', function () {
    Event::fake();

    User::factory()->create([ // assumes RefreshDatabase trait is used on Pest.php...
        'email' => 'nuno@laravel.com',
        'password' => 'password',
    ]);

    $page = visit('/')->on()->mobile()->firefox();

    $page->click('Sign In')
         ->assertUrlIs('/login')
         ->assertSee('Sign In to Your Account')
         ->fill('email', 'nuno@laravel.com')
         ->fill('password', 'password')
         ->click('Submit')
         ->assertSee('Dashboard');

    $this->assertAuthenticated();

    Event::assertDispatched(UserLoggedIn::class);
});
```

Note that, you are leveraging the full power of Laravel's testing capabilities, such as refresh database, event faking, and authentication assertions, while also actually doing browser testing.

## Getting Started

To get started with browser testing in Pest, you need to install the Pest Browser plugin. You can do this by running the following command:

```bash
composer require pestphp/pest-plugin-browser --dev

npm install playwright@latest
npx playwright install
```

Finally, add `tests/Browser/Screenshots` to your `.gitignore` file to avoid committing screenshots taken during browser tests.

### Running Browser Tests

Running browser tests is similar to running regular Pest tests:

```bash
./vendor/bin/pest
```

We recommend running tests in parallel using the `--parallel` option to speed up the execution:

```bash
./vendor/bin/pest --parallel
```

For debugging purposes, you can run the tests in a headed mode and pause the execution at the end of the failed test run:

```bash
./vendor/bin/pest --debug
```

### Navigation

The `visit()` method is used to navigate to a specific URL in your browser test. It provides various methods to interact with the page:

```php
test('example', function () {
    $page = visit('/');

    $page->assertSee('Welcome');
});
```

### Using Other Browsers

By default, the `visit()` method uses Chrome as the browser. However, if you want to use a different browser, you can specify it using the `--browser` option when running the tests:

```bash
./vendor/bin/pest --browser firefox
./vendor/bin/pest --browser safari
```

If you wish to use a different browser by default without specifying it in the command line, you can set it in your `Pest.php` configuration file:

```php
pest()->browser()->inFirefox();
pest()->browser()->inSafari();
```

### Using Other Devices

The `visit()` method uses a desktop viewport. However, you can specify a mobile viewport using the `onMobile()` method. For example:

```php
$page = visit('/')->on()->mobile();
```

If you wish to use a specific device, you can use the `on()` method and chain it with the `macbook14`, `iPhone14Pro`, etc:

```php
$page = visit('/')->on()->iPhone14Pro();
```

### Using Dark Mode

Pest enforces a light color scheme by default. However, you can specify a dark color scheme using the `inDarkMode()` method:

```php
$page = visit('/')->inDarkMode();
```

### Visiting Multiple Pages

You can visit multiple pages simultaneously by passing an array of URLs to the `visit()` method. This is useful for testing scenarios where you need to interact with multiple pages at once:

```php
$pages = visit(['/', '/about']);

$pages->assertNoSmoke()
    ->assertNoAccessibilityIssues()
    ->assertNoConsoleLogs()
    ->assertNoJavaScriptErrors();

[$homePage, $aboutPage] = $pages;

$homePage->assertSee('Welcome to our website');
$aboutPage->assertSee('About Us');
```

### Navigation

After visiting a page, you can navigate to other pages using the `navigate()` method. This method allows you to navigate to a different URL while keeping the current browser context:

```php
$page = visit('/');

$page->navigate('/about')
     ->assertSee('About Us');
```

### Locating Elements

You can locate elements in the DOM using text or CSS selectors. Pest provides a simple syntax for locating elements:

```php
// Clicks the first link with the text "Login"
$page->click('Login');

// Clicks the first element with the class "btn-primary"
$page->click('.btn-primary');

// Clicks the element with the data-test attribute "login"
$page->click('@login');

// Clicks the element with the ID "submit-button"
$page->click('#submit-button');

// etc...
```

### Configuring Timeouts

Sometimes, elements may take time to appear on the page. By default, Pest waits for `5` seconds before timing out. You can configure the default timeout for browser tests in your `Pest.php` configuration file:

```php
pest()->browser()->timeout(10000);
```

### Configuring User Agent

By default, the User Agent will default to the Browser you're running for tests such as: `Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) HeadlessChrome/133.0.6943.16 Safari/537.36` 

You may wish to override the User Agent of the browser for all of your tests, you can configure this in the `Pest.php` configuration file:

```php
pest()->browser()->userAgent('CustomUserAgent');
```

### Geolocation

Sometimes, you need to define where the browser believes it is physically on the earth.  This method takes a latitude and longitude and will set the `geolocation` permission in the browser and then make the coordinates available via Javascript's getCurrentPosition API:

```php
$page = visit('/')
     ->geolocation(39.399872, -8.224454);
```

### Configuring Locale

You can set the locale for your test requests using the `withLocale` method. This is particularly useful for testing multilingual applications.

```php
$page = visit('/')->withLocale('fr-FR');

$page->assertSee('Bienvenue');
```
### Configuring Timezone

You can set the timezone for your test requests using the `withTimezone` method. This is useful for testing date and time displays in different time zones.

```php
$page = visit('/')->withTimezone('America/New_York');

$page->assertSee('EST');
```

### Configuring UserAgent

You can set the User-Agent header for your test requests using the `withUserAgent` method. This is useful for testing how your application responds to different types of clients, such as mobile browsers or bots.

```php
$page = visit('/')->withUserAgent('Googlebot');

$page->assertSee('Welcome, bot!');
```

## Table of Contents

### Available Assertions

<div class="collection-method-list" markdown="1">

[assertTitle](#assert-title)
[assertTitleContains](#assert-title-contains)
[assertSee](#assert-see)
[assertDontSee](#assert-dont-see)
[assertSeeIn](#assert-see-in)
[assertDontSeeIn](#assert-dont-see-in)
[assertSeeAnythingIn](#assert-see-anything-in)
[assertSeeNothingIn](#assert-see-nothing-in)
[assertCount](#assert-count)
[assertScript](#assert-script)
[assertSourceHas](#assert-source-has)
[assertSourceMissing](#assert-source-missing)
[assertSeeLink](#assert-see-link)
[assertDontSeeLink](#assert-dont-see-link)
[assertChecked](#assert-checked)
[assertNotChecked](#assert-not-checked)
[assertIndeterminate](#assert-indeterminate)
[assertRadioSelected](#assert-radio-selected)
[assertRadioNotSelected](#assert-radio-not-selected)
[assertSelected](#assert-selected)
[assertNotSelected](#assert-not-selected)
[assertValue](#assert-value)
[assertValueIsNot](#assert-value-is-not)
[assertAttribute](#assert-attribute)
[assertAttributeMissing](#assert-attribute-missing)
[assertAttributeContains](#assert-attribute-contains)
[assertAttributeDoesntContain](#assert-attribute-doesnt-contain)
[assertAriaAttribute](#assert-aria-attribute)
[assertDataAttribute](#assert-data-attribute)
[assertVisible](#assert-visible)
[assertPresent](#assert-present)
[assertNotPresent](#assert-not-present)
[assertMissing](#assert-missing)
[assertEnabled](#assert-enabled)
[assertDisabled](#assert-disabled)
[assertButtonEnabled](#assert-button-enabled)
[assertButtonDisabled](#assert-button-disabled)
[assertUrlIs](#assert-url-is)
[assertSchemeIs](#assert-scheme-is)
[assertSchemeIsNot](#assert-scheme-is-not)
[assertHostIs](#assert-host-is)
[assertHostIsNot](#assert-host-is-not)
[assertPortIs](#assert-port-is)
[assertPortIsNot](#assert-port-is-not)
[assertPathBeginsWith](#assert-path-begins-with)
[assertPathEndsWith](#assert-path-ends-with)
[assertPathContains](#assert-path-contains)
[assertPathIs](#assert-path-is)
[assertPathIsNot](#assert-path-is-not)
[assertQueryStringHas](#assert-query-string-has)
[assertQueryStringMissing](#assert-query-string-missing)
[assertFragmentIs](#assert-fragment-is)
[assertFragmentBeginsWith](#assert-fragment-begins-with)
[assertFragmentIsNot](#assert-fragment-is-not)
[assertNoSmoke](#assert-no-smoke)
[assertNoConsoleLogs](#assert-no-console-logs)
[assertNoJavaScriptErrors](#assert-no-javascript-errors)
[assertNoAccessibilityIssues](#assert-no-accessibility-issues)
[assertScreenshotMatches](#assert-screenshot-matches)

</div>

### Element Interactions

<div class="collection-method-list" markdown="1">

[click](#click)
[text](#text)
[attribute](#attribute)
[keys](#keys)
[withKeyDown](#withKeyDown)
[type](#type)
[typeSlowly](#type-slowly)
[select](#select)
[append](#append)
[clear](#clear)
[radio](#radio)
[check](#check)
[uncheck](#uncheck)
[attach](#attach)
[press](#press)
[pressAndWaitFor](#press-and-wait-for)
[drag](#drag)
[hover](#hover)
[submit](#submit)
[value](#value)
[withinIframe](#within-iframe)
[resize](#resize)
[script](#script)
[content](#content)
[url](#url)
[wait](#wait)
[waitForKey](#wait-for-key)

</div>

### Debugging tests

<div class="collection-method-list" markdown="1">

[debug](#debug)
[screenshot](#screenshot)
[screenshotElement](#screenshot-element)
[tinker](#tinker)
[headed](#headed)

</div>

## Element Assertions

<a name="assert-title"></a>
### assertTitle

The `assertTitle` method asserts that the page title matches the given text:

```php
$page->assertTitle('Home Page');
```

<a name="assert-title-contains"></a>
### assertTitleContains

The `assertTitleContains` method asserts that the page title contains the given text:

```php
$page->assertTitleContains('Home');
```

<a name="assert-see"></a>
### assertSee

The `assertSee` method asserts that the given text is present on the page:

```php
$page->assertSee('Welcome to our website');
```

<a name="assert-dont-see"></a>
### assertDontSee

The `assertDontSee` method asserts that the given text is not present on the page:

```php
$page->assertDontSee('Error occurred');
```

<a name="assert-see-in"></a>
### assertSeeIn

The `assertSeeIn` method asserts that the given text is present within the selector:

```php
$page->assertSeeIn('.header', 'Welcome');
```

<a name="assert-dont-see-in"></a>
### assertDontSeeIn

The `assertDontSeeIn` method asserts that the given text is not present within the selector:

```php
$page->assertDontSeeIn('.error-container', 'Error occurred');
```

<a name="assert-see-anything-in"></a>
### assertSeeAnythingIn

The `assertSeeAnythingIn` method asserts that any text is present within the selector:

```php
$page->assertSeeAnythingIn('.content');
```

<a name="assert-see-nothing-in"></a>
### assertSeeNothingIn

The `assertSeeNothingIn` method asserts that no text is present within the selector:

```php
$page->assertSeeNothingIn('.empty-container');
```

<a name="assert-count"></a>
### assertCount

The `assertCount` method asserts that a given element is present a given amount of times:

```php
$page->assertCount('.item', 5);
```

<a name="assert-script"></a>
### assertScript

The `assertScript` method asserts that the given JavaScript expression evaluates to the given value:

```php
$page->assertScript('document.title', 'Home Page');
$page->assertScript('document.querySelector(".btn").disabled', true);
```

<a name="assert-source-has"></a>
### assertSourceHas

The `assertSourceHas` method asserts that the given source code is present on the page:

```php
$page->assertSourceHas('<h1>Welcome</h1>');
```

<a name="assert-source-missing"></a>
### assertSourceMissing

The `assertSourceMissing` method asserts that the given source code is not present on the page:

```php
$page->assertSourceMissing('<div class="error">');
```

<a name="assert-see-link"></a>
### assertSeeLink

The `assertSeeLink` method asserts that the given link is present on the page:

```php
$page->assertSeeLink('About Us');
```

<a name="assert-dont-see-link"></a>
### assertDontSeeLink

The `assertDontSeeLink` method asserts that the given link is not present on the page:

```php
$page->assertDontSeeLink('Admin Panel');
```

<a name="assert-checked"></a>
### assertChecked

The `assertChecked` method asserts that the given checkbox is checked:

```php
$page->assertChecked('terms');
$page->assertChecked('color', 'blue'); // For checkbox with specific value
```

<a name="assert-not-checked"></a>
### assertNotChecked

The `assertNotChecked` method asserts that the given checkbox is not checked:

```php
$page->assertNotChecked('newsletter');
$page->assertNotChecked('color', 'red'); // For checkbox with specific value
```

<a name="assert-indeterminate"></a>
### assertIndeterminate

The `assertIndeterminate` method asserts that the given checkbox is in an indeterminate state:

```php
$page->assertIndeterminate('partial-selection');
```

<a name="assert-radio-selected"></a>
### assertRadioSelected

The `assertRadioSelected` method asserts that the given radio field is selected:

```php
$page->assertRadioSelected('size', 'large');
```

<a name="assert-radio-not-selected"></a>
### assertRadioNotSelected

The `assertRadioNotSelected` method asserts that the given radio field is not selected:

```php
$page->assertRadioNotSelected('size', 'small');
```

<a name="assert-selected"></a>
### assertSelected

The `assertSelected` method asserts that the given dropdown has the given value selected:

```php
$page->assertSelected('country', 'US');
```

<a name="assert-not-selected"></a>
### assertNotSelected

The `assertNotSelected` method asserts that the given dropdown does not have the given value selected:

```php
$page->assertNotSelected('country', 'UK');
```

<a name="assert-value"></a>
### assertValue

The `assertValue` method asserts that the element matching the given selector has the given value:

```php
$page->assertValue('input[name=email]', 'test@example.com');
```

<a name="assert-value-is-not"></a>
### assertValueIsNot

The `assertValueIsNot` method asserts that the element matching the given selector does not have the given value:

```php
$page->assertValueIsNot('input[name=email]', 'invalid@example.com');
```

<a name="assert-attribute"></a>
### assertAttribute

The `assertAttribute` method asserts that the element matching the given selector has the given value in the provided attribute:

```php
$page->assertAttribute('img', 'alt', 'Profile Picture');
```

<a name="assert-attribute-missing"></a>
### assertAttributeMissing

The `assertAttributeMissing` method asserts that the element matching the given selector is missing the provided attribute:

```php
$page->assertAttributeMissing('button', 'disabled');
```

<a name="assert-attribute-contains"></a>
### assertAttributeContains

The `assertAttributeContains` method asserts that the element matching the given selector contains the given value in the provided attribute:

```php
$page->assertAttributeContains('div', 'class', 'container');
```

<a name="assert-attribute-doesnt-contain"></a>
### assertAttributeDoesntContain

The `assertAttributeDoesntContain` method asserts that the element matching the given selector does not contain the given value in the provided attribute:

```php
$page->assertAttributeDoesntContain('div', 'class', 'hidden');
```

<a name="assert-aria-attribute"></a>
### assertAriaAttribute

The `assertAriaAttribute` method asserts that the element matching the given selector has the given value in the provided aria attribute:

```php
$page->assertAriaAttribute('button', 'label', 'Close');
```

<a name="assert-data-attribute"></a>
### assertDataAttribute

The `assertDataAttribute` method asserts that the element matching the given selector has the given value in the provided data attribute:

```php
$page->assertDataAttribute('div', 'id', '123');
```

<a name="assert-visible"></a>
### assertVisible

The `assertVisible` method asserts that the element matching the given selector is visible:

```php
$page->assertVisible('.alert');
```

<a name="assert-present"></a>
### assertPresent

The `assertPresent` method asserts that the element matching the given selector is present in the DOM:

```php
$page->assertPresent('form');
```

<a name="assert-not-present"></a>
### assertNotPresent

The `assertNotPresent` method asserts that the element matching the given selector is not present in the DOM:

```php
$page->assertNotPresent('.error-message');
```

<a name="assert-missing"></a>
### assertMissing

The `assertMissing` method asserts that the element matching the given selector is not visible:

```php
$page->assertMissing('.hidden-element');
```

<a name="assert-enabled"></a>
### assertEnabled

The `assertEnabled` method asserts that the given field is enabled:

```php
$page->assertEnabled('email');
```

<a name="assert-disabled"></a>
### assertDisabled

The `assertDisabled` method asserts that the given field is disabled:

```php
$page->assertDisabled('submit');
```

<a name="assert-button-enabled"></a>
### assertButtonEnabled

The `assertButtonEnabled` method asserts that the given button is enabled:

```php
$page->assertButtonEnabled('Save');
```

<a name="assert-button-disabled"></a>
### assertButtonDisabled

The `assertButtonDisabled` method asserts that the given button is disabled:

```php
$page->assertButtonDisabled('Submit');
```

## URL Assertions

<a name="assert-url-is"></a>
### assertUrlIs

The `assertUrlIs` method asserts that the current URL matches the given string:

```php
$page->assertUrlIs('https://example.com/home');
```

<a name="assert-scheme-is"></a>
### assertSchemeIs

The `assertSchemeIs` method asserts that the current URL scheme matches the given scheme:

```php
$page->assertSchemeIs('https');
```

<a name="assert-scheme-is-not"></a>
### assertSchemeIsNot

The `assertSchemeIsNot` method asserts that the current URL scheme does not match the given scheme:

```php
$page->assertSchemeIsNot('http');
```

<a name="assert-host-is"></a>
### assertHostIs

The `assertHostIs` method asserts that the current URL host matches the given host:

```php
$page->assertHostIs('example.com');
```

<a name="assert-host-is-not"></a>
### assertHostIsNot

The `assertHostIsNot` method asserts that the current URL host does not match the given host:

```php
$page->assertHostIsNot('wrong-domain.com');
```

<a name="assert-port-is"></a>
### assertPortIs

The `assertPortIs` method asserts that the current URL port matches the given port:

```php
$page->assertPortIs('443');
```

<a name="assert-port-is-not"></a>
### assertPortIsNot

The `assertPortIsNot` method asserts that the current URL port does not match the given port:

```php
$page->assertPortIsNot('8080');
```

<a name="assert-path-begins-with"></a>
### assertPathBeginsWith

The `assertPathBeginsWith` method asserts that the current URL path begins with the given path:

```php
$page->assertPathBeginsWith('/users');
```

<a name="assert-path-ends-with"></a>
### assertPathEndsWith

The `assertPathEndsWith` method asserts that the current URL path ends with the given path:

```php
$page->assertPathEndsWith('/profile');
```

<a name="assert-path-contains"></a>
### assertPathContains

The `assertPathContains` method asserts that the current URL path contains the given path:

```php
$page->assertPathContains('settings');
```

<a name="assert-path-is"></a>
### assertPathIs

The `assertPathIs` method asserts that the current path matches the given path:

```php
$page->assertPathIs('/dashboard');
```

<a name="assert-path-is-not"></a>
### assertPathIsNot

The `assertPathIsNot` method asserts that the current path does not match the given path:

```php
$page->assertPathIsNot('/login');
```

<a name="assert-query-string-has"></a>
### assertQueryStringHas

The `assertQueryStringHas` method asserts that the given query string parameter is present and has a given value:

```php
$page->assertQueryStringHas('page');
$page->assertQueryStringHas('page', '2');
```

<a name="assert-query-string-missing"></a>
### assertQueryStringMissing

The `assertQueryStringMissing` method asserts that the given query string parameter is missing:

```php
$page->assertQueryStringMissing('page');
```

<a name="assert-fragment-is"></a>
### assertFragmentIs

The `assertFragmentIs` method asserts that the URL's current hash fragment matches the given fragment:

```php
$page->assertFragmentIs('section-2');
```

<a name="assert-fragment-begins-with"></a>
### assertFragmentBeginsWith

The `assertFragmentBeginsWith` method asserts that the URL's current hash fragment begins with the given fragment:

```php
$page->assertFragmentBeginsWith('section');
```

<a name="assert-fragment-is-not"></a>
### assertFragmentIsNot

The `assertFragmentIsNot` method asserts that the URL's current hash fragment does not match the given fragment:

```php
$page->assertFragmentIsNot('wrong-section');
```

## Console Assertions

<a name="assert-no-smoke"></a>
### assertNoSmoke

The `assertNoSmoke` method asserts there are no console logs or JavaScript errors on the page:

```php
$page->assertNoSmoke();
```

<a name="assert-no-console-logs"></a>
### assertNoConsoleLogs

The `assertNoConsoleLogs` method asserts there are no console logs on the page:

```php
$page->assertNoConsoleLogs();
```

<a name="assert-no-javascript-errors"></a>
### assertNoJavaScriptErrors

The `assertNoJavaScriptErrors` method asserts there are no JavaScript errors on the page:

```php
$page->assertNoJavaScriptErrors();
```

<a name="assert-no-accessibility-issues"></a>
### assertNoAccessibilityIssues

The `assertNoAccessibilityIssues` method asserts there are no "serious" accessibility issues on the page:

```php
$page->assertNoAccessibilityIssues();
```

By default, the level is 1 (serious). You can change to one of the following levels:

```
0. Critical
1. Serious
2. Moderate
3. Minor
```

- The level 0 (critical) only reports issues that cause severe barriers for individuals with disabilities. The organization may be subject to legal action if these issues are not addressed.
- The level 1 (serious) includes all critical issues (level 0) and adds issues that significantly impact accessibility. The organization may be subject to legal action if these issues are not addressed.
- The level 2 (moderate) includes all serious issues (level 1) and adds issues that moderately affect accessibility. The end-user would appreciate the fix, but it is not a barrier.
- The level 3 (minor) includes all moderate issues (level 2) and adds issues that have a minor impact on accessibility. These issues are often related to best practices and do not significantly affect the user experience.

## Screenshot Assertions

<a name="assert-screenshot-matches"></a>
### assertScreenshotMatches

The `assertScreenshotMatches` method asserts that the screenshot matches the expected image:

```php
$page->assertScreenshotMatches();
$page->assertScreenshotMatches(true, true); // Full page, show diff
```

## Element Interactions

<a name="click"></a>
### click

The `click` method clicks the link with the given text:

```php
$page->click('Login');
```

You may also pass options:

```php
$page->click('#button', options: ['clickCount' => 2]);
```

<a name="text"></a>
### text

The `text` method gets the text of the element matching the given selector:

```php
$text = $page->text('.header');
```

<a name="attribute"></a>
### attribute

The `attribute` method gets the given attribute from the element matching the given selector:

```php
$alt = $page->attribute('img', 'alt');
```

<a name="keys"></a>
### keys

The `keys` method sends the given keys to the element matching the given selector:

```php
$page->keys('input[name=password]', 'secret');
$page->keys('input[name=password]', ['{Control}', 'a']); // Keyboard shortcuts
```

<a name="withKeyDown"></a>
### withKeyDown

The `withKeyDown` method executes the given callback while a key is held down:

```php
$page->withKeyDown('Shift', function () use ($page): void {
    $page->keys('#input', ['KeyA', 'KeyB', 'KeyC']);
}); // writes "ABC"
```

> Note: To respect held keys like Shift, use key codes such as KeyA, KeyB, KeyC.
> 'a' always types a lowercase “a” and 'A' always types an uppercase “A”, regardless of modifiers.

<a name="type"></a>
### type

The `type` method types the given value in the given field:

```php
$page->type('email', 'test@example.com');
```

<a name="type-slowly"></a>

### typeSlowly

The `typeSlowly` method types the given value in the given field slowly, like a user:

```php
$page->typeSlowly('email', 'test@example.com');
```

<a name="select"></a>
### select

The `select` method selects the given value in the given field:

```php
$page->select('country', 'US');
$page->select('interests', ['music', 'sports']); // Multiple select
```

<a name="append"></a>
### append

The `append` method types the given value in the given field without clearing it:

```php
$page->append('description', ' Additional information.');
```

<a name="clear"></a>
### clear

The `clear` method clears the given field:

```php
$page->clear('search');
```

<a name="radio"></a>
### radio

The `radio` method selects the given value of a radio button field:

```php
$page->radio('size', 'large');
```

<a name="check"></a>
### check

The `check` method checks the given checkbox:

```php
$page->check('terms');
$page->check('color', 'blue'); // For checkbox with specific value
```

<a name="uncheck"></a>
### uncheck

The `uncheck` method unchecks the given checkbox:

```php
$page->uncheck('newsletter');
$page->uncheck('color', 'red'); // For checkbox with specific value
```

<a name="attach"></a>
### attach

The `attach` method attaches the given file to the field:

```php
$page->attach('avatar', '/path/to/image.jpg');
```

<a name="press"></a>
### press

The `press` method presses the button with the given text or name:

```php
$page->press('Submit');
```

<a name="press-and-wait-for"></a>
### pressAndWaitFor

The `pressAndWaitFor` method presses the button with the given text or name and waits for a specified amount of time:

```php
$page->pressAndWaitFor('Submit', 2); // Wait for 2 seconds
```

<a name="drag"></a>
### drag

The `drag` method drags an element to another element using selectors:

```php
$page->drag('#item', '#target');
```

<a name="hover"></a>
### hover

The `hover` method hovers over the given element:

```php
$page->hover('#item');
```

<a name="submit"></a>
### submit

The `submit` method submits the first form found on the page:

```php
$page->submit();
```

<a name="value"></a>
### value

The `value` method gets the value of the element matching the given selector:

```php
$value = $page->value('input[name=email]');
```

<a name="with-in-iframe"></a>

### withinIframe

The `withinIframe` method allows you to interact with elements inside an iframe:

```php
use Pest\Browser\Api\PendingAwaitablePage;

$page->withinIframe('.iframe-container', function (PendingAwaitablePage $page) {
    $page->type('frame-input', 'Hello iframe')
        ->click('frame-button');
});
```

<a name="resize"></a>

### resize

You may use the resize method to adjust the size of the browser window:

```php
$page->resize(1280, 720);
```

<a name="script"></a>
### script

The `script` method executes a script in the context of the page:

```php
$result = $page->script('document.title');
```

<a name="content"></a>
### content

The `content` method gets the page's content:

```php
$html = $page->content();
```

<a name="url"></a>
### url

The `url` method gets the page's URL:

```php
$currentUrl = $page->url();
```

<a name="wait"></a>
### wait

The `wait` method pauses for the given number of seconds:

```php
$page->wait(2); // Wait for 2 seconds
```

<a name="wait-for-key"></a>
### waitForKey

The `waitForKey` method opens the current page URL in the default web browser and waits for a key press:

```php
$page->waitForKey(); // Useful for debugging
```

## Debugging tests

<a name="debug"></a>
Sometimes you may want to debug your browser tests. Pest provides a convenient way to do this by using the `--debug` option, which makes pest to open the browser window and pause the execution of the test when it fails. You can then inspect the page and see what went wrong.

```bash
./vendor/bin/pest --debug
```

Optionally, you can also use the `debug()` method in your test. It will limit execution to this test (like using [`only()`](filtering-tests.md#only)), pause the execution and open the browser window:

```php
$page->debug();
```


<a name="screenshot"></a>
You can also take a screenshot of the current page using the `screenshot()` method. This is useful for visual debugging:

NOTE: If you don't pass the filename, it will use the test name as the filename.

```php
$page->screenshot();
$page->screenshot(fullPage: true);
$page->screenshot(filename: 'custom-name');
```

<a name="screenshotElement"></a>
You can also take a screenshot of a specific element using the `screenshotElement()` method:

```php
$page->screenshotElement('#my-element');
```

<a name="tinker"></a>
You can also use the `tinker()` method to open a Tinker session in the context of the current page. This allows you to interact with the page using PHP code:

```php
$page->tinker();
```

<a name="headed"></a>
After you can run your tests with the `--headed` option to open the browser window:

```bash
./vendor/bin/pest --headed
```

If you wish to run the tests in a headed mode by default, you can set it in your `Pest.php` configuration file:

```php
pest()->browser()->headed();
```

## Continuous Integration

You may refer to Pest's [Continuous Integration](https://pestphp.com/docs/continuous-integration) documentation for more information on how to run your browser tests in a CI environment.

However, if you are using GitHub Actions, you need to add the following steps to your workflow file:

```yaml
    - uses: actions/setup-node@v4
      with:
        node-version: lts/*

    - name: Install dependencies
      run: npm ci

    - name: Install Playwright Browsers
      run: npx playwright install --with-deps
```

---

Now, let's dive into architectural testing and how it can benefit your development process. By performing architectural testing, you can evaluate the overall design of your application and identify potential flaws before they become significant issues: [Arch Testing](/docs/arch-testing)
