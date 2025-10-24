---
title: Browser Testing
description: Browser testing enables you to automate web application interactions in real browsers, helping you validate functionality across different browsers and devices.
---

# Browser Testing

Pest's browser testing plugin provides a powerful API for automating browser interactions. Here's everything you need to know to get started:

```bash
# Installation
composer require pestphp/pest-plugin-browser --dev
npm install playwright@latest
npx playwright install
```

## Basic Usage

```php
test('user can log in', function () {
    visit('/login')
        ->type('email', 'test@example.com')
        ->type('password', 'password')
        ->click('Login')
        ->assertPathIs('/dashboard');
});
```

## Available Methods

<div class="collection-method-list" markdown="1">

### Navigation

- [visit()](#visit) - Visit a URL
- [navigate()](#navigate) - Navigate to another URL
- [back()](#back) - Go back
- [forward()](#forward) - Go forward
- [refresh()](#refresh) - Refresh page

### Interactions

- [click()](#click) - Click element
- [type()](#type) - Type text
- [select()](#select) - Select option
- [check()](#check) - Check checkbox
- [uncheck()](#uncheck) - Uncheck checkbox
- [attach()](#attach) - Attach file
- [press()](#press) - Press key
- [hover()](#hover) - Hover over element
- [drag()](#drag) - Drag element

### Assertions

- [assertSee()](#assert-see) - Assert text visible
- [assertDontSee()](#assert-dont-see) - Assert text not visible
- [assertTitle()](#assert-title) - Assert page title
- [assertUrlIs()](#assert-url-is) - Assert current URL
- [assertPathIs()](#assert-path-is) - Assert current path
- [assertVisible()](#assert-visible) - Assert element visible
- [assertMissing()](#assert-missing) - Assert element not visible
- [assertValue()](#assert-value) - Assert input value

### Device & Network

- [mobile()](#mobile) - Mobile viewport
- [tablet()](#tablet) - Tablet viewport
- [desktop()](#desktop) - Desktop viewport
- [setNetwork()](#set-network) - Set network conditions
- [setGeolocation()](#set-geolocation) - Set location

### Debug & Screenshots

- [screenshot()](#screenshot) - Take screenshot
- [debug()](#debug) - Start debugging
- [pause()](#pause) - Pause execution

</div>

## Common Examples

### Form Testing

```php
test('contact form submission', function () {
    visit('/contact')
        ->type('name', 'John Doe')
        ->type('email', 'john@example.com')
        ->type('message', 'Hello')
        ->attach('attachment', '/path/to/file.pdf')
        ->click('Submit')
        ->assertSee('Message sent successfully');
});
```

### Responsive Testing

```php
test('menu is responsive', function () {
    visit('/')
        ->desktop()
        ->assertVisible('#desktop-menu')
        ->assertMissing('#mobile-menu')
        ->mobile()
        ->assertVisible('#mobile-menu')
        ->assertMissing('#desktop-menu');
});
```

### Multi-page Testing

```php
test('multiple pages', function () {
    $pages = visit(['/', '/about']);
    [$home, $about] = $pages;

    $home->assertSee('Welcome');
    $about->assertSee('About Us');
});
```

### Error Handling

```php
test('form validation', function () {
    visit('/register')
        ->click('Register')
        ->assertSee('Email is required')
        ->type('email', 'invalid')
        ->click('Register')
        ->assertSee('Valid email required')
        ->assertPathIs('/register');
});
```

## Configuration

Configure browser testing in your `Pest.php`:

- `inChrome()` - Use Chrome (default)
- `inFirefox()` - Use Firefox
- `timeout(10000)` - Set timeout (ms)
- `headed()` - Show browser
- `headless()` - Hide browser (default)

```php
pest()->browser()
    ->inChrome()
    ->inFirefox()
    ->timeout(10000)
    ->headed()
    ->headless();
```

<div class="collection-method-list" markdown="1">

[Installation](#installation)
[Basic Usage](#basic-usage)
[Navigation](#navigation)
[Element Interactions](#element-interactions)
[Assertions](#assertions)
[Screenshots](#screenshots)
[Device Emulation](#device-emulation)
[Network Conditions](#network-conditions)
[Console and Errors](#console-and-errors)
[Frames and Windows](#frames-and-windows)
[Geolocation](#geolocation)
[Accessibility Testing](#accessibility-testing)

</div>

## Table of Contents

- [Getting Started](#getting-started)

  - [Installation](#installation)
  - [Basic Configuration](#basic-configuration)
  - [Running Tests](#running-tests)

- [Core Features](#core-features)

  - [Navigation and Page Interaction](#navigation-and-page-interaction)
  - [Screen Interactions](#screen-interactions)
  - [Frame Handling](#frame-handling)
  - [Tab Management](#tab-management)
  - [Viewport Control](#viewport-control)
  - [Toolbar Operations](#toolbar-operations)

- [Testing Capabilities](#testing-capabilities)

  - [Element Interactions](#element-interactions)
  - [Form Handling](#form-handling)
  - [Mouse and Keyboard](#mouse-and-keyboard)
  - [Screenshots](#screenshots)
  - [Console and Network](#console-and-network)

- [Assertions](#assertions)

  - [Element Assertions](#element-assertions)
  - [URL Assertions](#url-assertions)
  - [Console Assertions](#console-assertions)
  - [Screenshot Assertions](#screenshot-assertions)

- [Advanced Features](#advanced-features)

  - [Multiple Page Testing](#multiple-page-testing)
  - [Location Simulation](#location-simulation)
  - [Device Emulation](#device-emulation)
  - [Livewire Support](#livewire-support)

- [Configuration](#configuration)

  - [Browser Options](#browser-options)
  - [Viewport Settings](#viewport-settings)
  - [Network Conditions](#network-conditions)
  - [Screenshot Settings](#screenshot-settings)

- [Debugging](#debugging)
  - [Visual Debugging](#visual-debugging)
  - [Console Debugging](#console-debugging)
  - [Network Debugging](#network-debugging)

## Getting Started

<a name="installation"></a>

### Installation

Browser testing in Pest requires both the Pest Browser plugin and Playwright. You can install them using the following commands:

```bash
# Install the browser plugin
composer require pestphp/pest-plugin-browser --dev

# Install Playwright
npm install playwright@latest

# Install browser drivers
npx playwright install
```

After installation, add the following path to your `.gitignore` file to exclude browser test screenshots:

```
tests/Browser/screenshots/
```

### Basic Example

Here's a simple example of a browser test using Pest:

```php
test('homepage shows welcome message', function () {
    $page = visit('/');

    $page->assertSee('Welcome')
         ->assertTitle('Home')
         ->assertNoConsoleErrors();
});
```

For a more complex example, here's a Laravel authentication test:

```php
test('user can sign in', function () {
// Setup
Event::fake();
User::factory()->create([
'email' => 'test@example.com',
'password' => 'password',
]);

    // Test
    $page = visit('/')->on()->mobile()->firefox();

    $page->click('Sign In')
         ->assertUrlIs('/login')
         ->assertSee('Sign In to Your Account')
         ->fill('email', 'test@example.com')
         ->fill('password', 'password')
         ->click('Submit')
         ->assertSee('Dashboard');

    // Assertions
    expect()->toBeAuthenticated();
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

### Running Tests and Configuration

Available test running options:

- `./vendor/bin/pest` - Run all tests
- `./vendor/bin/pest --parallel` - Run tests in parallel
- `./vendor/bin/pest --debug` - Run in debug mode (headed browser)
- `./vendor/bin/pest --browser firefox` - Run with Firefox browser
- `./vendor/bin/pest --browser safari` - Run with Safari browser

### Multiple Page Testing

You can test multiple pages simultaneously using array capabilities:

```php
test('multiple pages', function () {
    // Visit multiple pages at once
    $pages = visit(['/', '/about', '/contact']);

    // Run assertions on all pages
    $pages->assertNoConsoleErrors()
          ->assertNoJavaScriptErrors();

    // Access individual pages
    [$home, $about, $contact] = $pages;

    $home->assertSee('Welcome');
    $about->assertPathIs('/about');
    $contact->assertTitle('Contact Us');
});
```

### Configuration Options

You can configure the browser behavior in your `Pest.php` file:

Browser selection:

- `inChrome()` - Use Chrome browser
- `inFirefox()` - Use Firefox browser
- `inSafari()` - Use Safari browser

Display modes:

- `headed()` - Show browser window
- `headless()` - Hide browser window
- `inDarkMode()` - Enable dark mode

Timing and behavior:

- `timeout(10000)` - Set timeout in milliseconds
- `userAgent('Custom/1.0')` - Set custom user agent

Screenshot configuration:

- `screenshotsPath('tests/Browser/screenshots')` - Set screenshots path
- `failedScreenshotsPath('tests/Browser/screenshots/failures')` - Set failed screenshots path

```php
use Pest\Plugin\Browser\Configuration;

pest()->browser()
    // Browser selection
    ->inChrome()
    ->inFirefox()
    ->inSafari()

    // Display modes
    ->headed()
    ->headless()
    ->inDarkMode()

    // Timing and behavior
    ->timeout(10000)
    ->userAgent('Custom/1.0')

    // Screenshot configuration
    ->screenshotsPath('tests/Browser/screenshots')
    ->failedScreenshotsPath('tests/Browser/screenshots/failures');
```

## Core Features

### Navigation and Page Interaction

The browser plugin provides various methods to interact with web pages:

``Browser navigation and interaction methods:

Navigation methods:

- `navigate('/about')` - Navigate to another URL
- `back()` - Go back one page
- `forward()` - Go forward one page
- `refresh()` - Refresh current page

Waiting methods:

- `waitForNavigation()` - Wait for page navigation
- `waitFor('.element')` - Wait for element
- `waitForText('Loading completed')` - Wait for text

Element interaction methods:

- `click('.button')` - Click an element
- `type('email', 'value')` - Type into input
- `fill('message', 'text')` - Fill form field
- `select('country', 'USA')` - Select dropdown option
- `check('terms')` - Check checkbox
- `uncheck('newsletter')` - Uncheck checkbox
- `press('Submit')` - Press button

```php
test('page navigation and interaction', function () {
    $page = visit('/')
        // Basic navigation
        ->navigate('/about')
        ->back()
        ->forward()
        ->refresh()

        // Waiting capabilities
        ->waitForNavigation()
        ->waitFor('.element')
        ->waitForText('Loading completed')

        // Element interaction
        ->click('.button')
        ->type('email', 'test@example.com')
        ->fill('message', 'Hello')
        ->select('country', 'USA')
        ->check('terms')
        ->uncheck('newsletter')
        ->press('Submit');
});
```

### Using Other Browsers

By default, the `visit()` method uses Chrome as the browser. Available browser options:

- `./vendor/bin/pest --browser firefox` - Run tests in Firefox
- `./vendor/bin/pest --browser safari` - Run tests in Safari

### Toolbar Operations (InteractsWithToolbar)

The Toolbar Operations feature provides methods to interact with browser's DevTools and various browser controls.

DevTools Operations:

- `openDevTools()` - Open Chrome DevTools
- `closeDevTools()` - Close DevTools

Navigation Controls:

- `reload()` - Reload the current page
- `reloadWithCache()` - Force reload with cache
- `reloadWithoutCache()` - Force reload bypassing cache

Browser Controls:

- `maximize()` - Maximize browser window
- `minimize()` - Minimize browser window
- `fullScreen()` - Enter full screen mode
- `exitFullScreen()` - Exit full screen mode

Network Operations:

- `setOffline(true)` - Enable offline mode
- `setOffline(false)` - Disable offline mode

Performance Controls:

- `emulateCPUThrottling(4)` - Simulate slower CPU
- `emulateNetworkConditions()` - Simulate network conditions

```php
$page = visit('/')
    // DevTools Operations
    ->openDevTools()
    ->closeDevTools()

    // Navigation Controls
    ->reload()
    ->reloadWithCache()
    ->reloadWithoutCache()

    // Browser Controls
    ->maximize()
    ->minimize()
    ->fullScreen()
    ->exitFullScreen()

    // Network Operations
    ->setOffline(true)
    ->setOffline(false)

    // Performance
    ->emulateCPUThrottling(4)
    ->emulateNetworkConditions([
        'offline' => false,
        'latency' => 100,
        'downloadThroughput' => 1024 * 1024,
        'uploadThroughput' => 1024 * 1024
    ]);

// Example with debugging workflow
$page->visit('/complex-page')
    ->openDevTools()
    ->assertSee('Loading...')
    ->waitForText('Loaded')
    ->screenshot('debug-view.png')
    ->closeDevTools();
```

Common Use Cases:

- Debugging complex interactions
- Testing offline behavior
- Performance testing
- Network condition testing
- Cache-related testing
- Responsive design testing

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

### Screen Interactions (InteractsWithScreen)

Screen Interactions provide powerful capabilities for capturing and validating visual aspects of your application, including screenshots and visual regression testing.

```php
$page = visit('/')
    // Basic Screenshot
    ->screenshot('homepage.png')

    // Full Page Screenshot
    ->screenshot('full-page.png', fullPage: true)

    // Screenshot with Specific Dimensions
    ->screenshot('custom-size.png', clip: [
        'x' => 0,
        'y' => 0,
        'width' => 1920,
        'height' => 1080
    ])

    // Element Screenshots
    ->screenshotElement('#header', 'header.png')
    ->screenshotElement('.product-card', 'product.png')

    // Screenshot with Options
    ->screenshot('homepage-dark.png', options: [
        'omitBackground' => true,
        'quality' => 80,
        'timeout' => 5000
    ])

    // Visual Regression Testing
    ->assertScreenshotMatches()  // Uses test name as baseline
    ->assertScreenshotMatches('custom-baseline.png')
    ->assertScreenshotMatches(fullPage: true)
    ->assertElementScreenshotMatches('#element', 'element-baseline.png')

    // Mobile/Responsive Screenshots
    ->on()->iPhone14()
    ->screenshot('mobile-view.png')
    ->on()->desktop()
    ->screenshot('desktop-view.png');

// Advanced Screenshot Testing
$page->visit('/products')
    // Take screenshots in different states
    ->screenshot('products-initial.png')
    ->click('#filter-button')
    ->waitForText('Filtered Results')
    ->screenshot('products-filtered.png')
    ->hover('.product-card')
    ->screenshot('product-hover.png');
```

Screenshot Options:

- `fullPage`: Capture entire scrollable page
- `clip`: Capture specific area
- `omitBackground`: Make background transparent
- `quality`: JPEG quality (0-100)
- `timeout`: Maximum time to wait for screenshot
- `type`: Image format (png/jpeg)

Visual Regression Features:

1. Automatic baseline creation
2. Pixel-by-pixel comparison
3. Tolerance settings for minor differences
4. Diff image generation
5. Multiple comparison strategies

Common Use Cases:

- Visual regression testing
- Documentation generation
- Bug reporting
- State verification
- Responsive design testing
- Cross-browser testing
- Animation testing (with multiple screenshots)

Best Practices:

1. Use consistent viewport sizes
2. Consider dynamic content
3. Handle loading states
4. Set appropriate tolerances
5. Organize screenshots by feature
6. Version control baselines
7. Regular baseline updates### Frame Handling (InteractsWithFrames)

The Frame Handling feature allows you to interact with content inside iframes. This is particularly useful when testing applications that embed third-party content or use frame-based layouts.

```php
$page = visit('/')
    // Basic frame interaction
    ->withinFrame('#iframe', function ($frame) {
        $frame->click('.button')
              ->assertSee('Frame Content')
              ->type('email', 'test@example.com');
    })

    // Multiple frames handling
    ->withinFrame(['#parent-frame', '#child-frame'], function ($frame) {
        // Interact with nested frames
        $frame->click('.nested-button');
    })

    // Frame by name or selector
    ->withinFrame('frame-name', function ($frame) {
        $frame->assertVisible('.frame-content');
    })

    // Wait for frame to be available
    ->waitForFrame('#loading-frame')

    // Assertions within frames
    ->withinFrame('#payment-frame', function ($frame) {
        $frame->assertVisible('#card-number')
              ->type('#card-number', '4242424242424242')
              ->assertValue('#card-number', '4242424242424242');
    });

// Example with complex frame interactions
$page->visit('/checkout')
    ->withinFrame('#payment-widget', function ($frame) {
        // Fill payment form in iframe
        $frame->type('cardNumber', '4242424242424242')
              ->type('expiryDate', '12/25')
              ->type('cvv', '123')
              ->press('Pay Now')
              ->waitForText('Payment Successful');
    })
    ->assertPathIs('/confirmation');
```

Frame Handling Features:

- Support for single and nested iframes
- Frame selection by CSS selector or name
- Automatic waiting for frame availability
- Full access to page interaction methods within frames
- Support for assertions within frames
- Ability to chain frame interactions
- Automatic frame context switching
- Error handling for missing frames

Common Use Cases:

1. Testing embedded payment forms
2. Interacting with rich text editors
3. Testing embedded maps or media players
4. Working with third-party widgets
5. Testing administrative dashboards with frame-based layouts

### Tab Management (InteractsWithTab)

Tab Management allows you to work with multiple browser tabs/windows, essential for testing scenarios that involve pop-ups, new window interactions, or multi-window workflows.

```php
$page = visit('/')
    // Basic Tab Operations
    ->openNewTab()                      // Opens a new empty tab
    ->switchToTab(1)                    // Switch to tab by index
    ->visit('/new-page')               // Navigate in new tab
    ->assertUrlIs('/new-page')
    ->closeTab()                        // Close current tab
    ->switchToTab(0)                    // Return to first tab

    // Advanced Tab Management
    ->openNewTab(url: '/direct-page')   // Open with specific URL
    ->waitForTab(2)                     // Wait for specific number of tabs
    ->getAllTabs()                      // Get all open tabs
    ->switchToLastTab()                 // Switch to most recent tab
    ->switchToFirstTab()                // Switch to first tab

    // Working with Multiple Tabs
    ->openNewTab()
    ->within(1, function ($tab) {       // Work in specific tab
        $tab->assertSee('New Tab Content');
    })

    // Handling Popup Windows
    ->click('#open-popup')
    ->waitForNewTab()
    ->switchToLastTab()
    ->assertSee('Popup Content')

    // Tab Context Preservation
    ->switchToTab(0)
    ->assertSee('Original Content');

// Complex Multi-tab Scenario
$page->visit('/products')
    // Open product in new tab
    ->click('#view-in-new-tab', modifiers: ['Control'])
    ->waitForNewTab()
    ->switchToLastTab()
    ->assertUrlContains('/product/')
    ->assertSee('Product Details')

    // Compare in multiple tabs
    ->switchToFirstTab()
    ->assertSee('Product List')

    // Clean up
    ->closeAllOtherTabs()              // Close all except current
    ->assertTabCount(1);               // Verify cleanup
```

Tab Management Features:

1. Create new tabs with or without URLs
2. Switch between tabs by index
3. Close individual or multiple tabs
4. Wait for tab operations
5. Handle popup windows
6. Preserve context across tabs
7. Access all open tabs
8. Tab count assertions

Common Use Cases:

- Testing "Open in New Tab" functionality
- Handling popup windows
- Multi-window workflows
- Compare views across tabs
- Testing tab-specific behavior
- Social media authentication flows
- Document preview features

Best Practices:

1. Always clean up tabs after tests
2. Use explicit waits for new tabs
3. Handle tab indices carefully
4. Verify correct context after switching
5. Consider tab lifecycle management
6. Handle unexpected popups
7. Test tab-specific keyboard shortcuts

### Viewport Control (InteractsWithViewPort)

The viewport control allows you to manipulate the browser's viewport size and emulate different devices. This is particularly useful for testing responsive designs and mobile-specific features.

```php
$page = visit('/')
    // Set specific dimensions
    ->resize(1920, 1080)
    ->setViewport([
        'width' => 1280,
        'height' => 720,
        'deviceScaleFactor' => 1,
        'isMobile' => false,
        'hasTouch' => false,
        'isLandscape' => false
    ])

    // Predefined device settings
    ->on()->iPhone14()
    ->on()->iPhone14Pro()
    ->on()->iPhone14ProMax()
    ->on()->macbook14()
    ->on()->macbook16()

    // Generic device types
    ->on()->mobile()
    ->on()->tablet()
    ->on()->desktop()

    // Orientation control
    ->on()->landscape()
    ->on()->portrait();

// Chain with other assertions
$page->assertVisible('.mobile-menu')    // On mobile view
    ->on()->desktop()                   // Switch to desktop
    ->assertMissing('.mobile-menu');    // Verify responsive behavior
```

Available Device Presets:

- Mobile Devices

  - `iPhone14()`
  - `iPhone14Plus()`
  - `iPhone14Pro()`
  - `iPhone14ProMax()`
  - `iPhone13()`
  - `iPhone13Pro()`
  - `iPhone13ProMax()`
  - `iPadAir()`
  - `iPadMini()`
  - `pixel7()`
  - `galaxyS23()`

- Desktop Devices
  - `macbook13()`
  - `macbook14()`
  - `macbook16()`
  - `desktop4K()`
  - `desktopHD()`

Each device preset automatically configures:

- Viewport dimensions
- Device scale factor
- User agent string
- Touch capability
- Mobile mode
- Default orientation

### Custom Viewport Settings

You can configure custom viewport settings using these options:

- `width` - Viewport width in pixels
- `height` - Viewport height in pixels
- `deviceScaleFactor` - Screen density (e.g., 2 for Retina displays)
- `isMobile` - Enable mobile device mode
- `hasTouch` - Enable touch screen capabilities
- `isLandscape` - Set landscape orientation

```php
$page->setViewport([
    'width' => 1024,
    'height' => 768,
    'deviceScaleFactor' => 2,
    'isMobile' => true,
    'hasTouch' => true,
    'isLandscape' => true
]);
```

### Console and Error Handling (MakesConsoleAssertions)

The Console and Error Handling feature allows you to monitor and assert browser console output, JavaScript errors, and other browser-level messages. This is crucial for detecting client-side issues and debugging JavaScript applications.

Available Console Message Types:

1. `log` - General logging messages
2. `info` - Informational messages
3. `warning` - Warning messages
4. `error` - Error messages
5. `debug` - Debug messages

Available Assertions:

- `assertNoConsoleErrors()` - No error messages
- `assertNoJavaScriptErrors()` - No JS exceptions
- `assertNoConsoleLogs()` - No console messages
- `assertConsoleLogContains()` - Check log content
- `assertConsoleWarningContains()` - Check warning content
- `assertConsoleErrorContains()` - Check error content
- `assertNoSmoke()` - No issues of any kind
- `assertNoSlowResources()` - Performance check
- `assertNoNetworkErrors()` - Network check

Example usage:

```php
$page = visit('/')
    // Basic Console Assertions
    ->assertNoConsoleErrors()              // No error-level messages
    ->assertNoJavaScriptErrors()           // No JavaScript exceptions
    ->assertNoConsoleLogs()                // No console messages at all

    // Console Message Type Assertions
    ->assertConsoleLogContains('Debug message')
    ->assertConsoleWarningContains('Warning')
    ->assertConsoleErrorContains('Error')

    // Multiple Message Assertions
    ->assertConsoleLogContains([
        'First message',
        'Second message'
    ])

    // Regex Pattern Matching
    ->assertConsoleLogMatches('/API response: .*/')

    // Combined Assertions
    ->assertNoSmoke()                      // No errors/warnings/logs

    // Timing and Performance
    ->assertNoSlowResources()              // Check for slow loading resources
    ->assertNoNetworkErrors()              // Check for failed network requests

    // Custom Console Handling
    ->tap(function ($page) {
        $consoleLogs = $page->getConsoleLogs();
        // Custom log processing
    });

// Example with error tracking
$page->visit('/js-heavy-page')
    ->waitForText('Loaded')
    ->tap(function ($page) {
        $errors = $page->getJavaScriptErrors();
        expect($errors)->toBeEmpty();
    });
```

Best Practices:

1. Clear console before critical assertions
2. Handle asynchronous console messages
3. Use specific assertions for message types
4. Consider environment-specific logging
5. Monitor resource loading errors
6. Track performance issues
7. Handle expected console output

### Element Assertions (MakesElementAssertions)

Element assertions allow you to verify the presence, visibility, and state of elements on the page. These assertions help ensure your page structure and content are correct.

Available Element Assertions:

- `assertVisible()` - Assert element is visible
- `assertHidden()` - Assert element is hidden
- `assertExists()` - Assert element exists in DOM
- `assertMissing()` - Assert element doesn't exist
- `assertValue()` - Assert input value matches
- `assertSelected()` - Assert option is selected
- `assertChecked()` - Assert checkbox is checked
- `assertNotChecked()` - Assert checkbox is not checked

Example usage:

```php
$page = visit('/')
    // Visibility assertions
    ->assertVisible('.element')            // Check if element is visible
    ->assertHidden('.hidden-element')      // Check if element is hidden

    // Existence assertions
    ->assertExists('#id')                  // Check if element exists in DOM
    ->assertMissing('.not-found')         // Check if element doesn't exist

    // Form element assertions
    ->assertValue('input[name="email"]', 'test@example.com')  // Check input value
    ->assertSelected('select[name="country"]', 'USA')         // Check selected option
    ->assertChecked('#terms')                                 // Check if checked
    ->assertNotChecked('#newsletter');                        // Check if unchecked
```

Best Practices:

1. Use specific selectors to avoid ambiguity
2. Check both positive and negative cases
3. Verify form states after interactions
4. Consider async behavior when checking visibility
5. Test both initial and changed states

### URL Assertions (MakesUrlAssertions)

```php
$page = visit('/')
    ->assertUrlIs('https://example.com')
    ->assertPathIs('/home')
    ->assertSchemeIs('https')
    ->assertHostIs('example.com')
    ->assertQueryStringHas('page', '1')
    ->assertQueryStringMissing('debug')
    ->assertFragmentIs('section-1');
```

### Location Simulation (From)

The Location Simulation feature allows you to test location-specific behavior by simulating different geographical locations and related settings.

```php
$page = visit('/')->from()
    // Predefined Locations
    ->amsterdam()                    // Simulate being in Amsterdam
    ->london()                       // Simulate being in London
    ->newYork()                     // Simulate being in New York
    ->tokyo()                       // Simulate being in Tokyo

    // Locale and Timezone
    ->withLocale('nl-NL')           // Set Dutch locale
    ->withTimezone('Europe/Amsterdam') // Set Amsterdam timezone

    // Custom Location
    ->location(52.3676, 4.9041)    // Custom coordinates

    // Combined Settings
    ->from()
        ->paris()
        ->withLocale('fr-FR')
        ->withTimezone('Europe/Paris')
        ->withCurrency('EUR');

// Example with location-based testing
$page->visit('/weather')
    ->from()->tokyo()
    ->assertSee('°C')               // Metric units
    ->assertSee('JST')              // Japan timezone

    ->from()->newYork()
    ->assertSee('°F')               // Imperial units
    ->assertSee('EST');             // US timezone
```

Available Features:

1. Predefined city locations
2. Custom GPS coordinates
3. Locale settings
4. Timezone configuration
5. Currency formatting
6. Language preferences
7. Regional settings

Predefined Locations:

- `amsterdam()` - Amsterdam, Netherlands
- `london()` - London, UK
- `newYork()` - New York, USA
- `tokyo()` - Tokyo, Japan
- `paris()` - Paris, France
- `berlin()` - Berlin, Germany
- And more...

Common Use Cases:

- Testing geo-restricted content
- Language/locale testing
- Timezone-specific features
- Currency formatting
- Regional pricing
- Content localization
- Weather applications
- Maps and navigation

### Livewire Support

The Livewire Support feature provides comprehensive testing capabilities for Laravel Livewire components, allowing you to interact with and assert against Livewire-specific functionality.

```php
$page = visit('/')
    // Basic Livewire Testing
    ->livewire()
    ->assertSeeText('Component content')
    ->assertSet('property', 'value')
    ->call('method')
    ->assertEmitted('event')

    // Component State
    ->assertSet('counter', 0)
    ->call('increment')
    ->assertSet('counter', 1)

    // Event Testing
    ->assertEmitted('counter-updated')
    ->assertEmittedUp('parent-notified')
    ->assertNotEmitted('unused-event')

    // Property Updates
    ->updateProperty('name', 'Test')
    ->assertSet('name', 'Test')

    // Form Interaction
    ->fillForm([
        'email' => 'test@example.com',
        'password' => 'secret'
    ])
    ->call('submit')

    // File Uploads
    ->upload('avatar', '/path/to/image.jpg')
    ->assertSet('hasAvatar', true)

    // Validation
    ->assertHasErrors(['email'])
    ->assertHasNoErrors(['name']);

// Example with complex Livewire interaction
$page->visit('/todos')
    ->livewire()
    ->assertSet('todos', [])
    ->updateProperty('newTodo', 'Buy groceries')
    ->call('addTodo')
    ->assertSet('todos', ['Buy groceries'])
    ->assertEmitted('todo-added')
    ->assertSeeText('Buy groceries');
```

Available Features:

1. Component state assertions
2. Method calling
3. Event handling
4. Property manipulation
5. Form interaction
6. File upload testing
7. Validation testing
8. Real-time updates

Common Assertions:

- `assertSet()` - Check property values
- `assertEmitted()` - Verify events
- `assertHasErrors()` - Validate form errors
- `assertSeeHtml()` - Check rendered HTML
- `assertDispatched()` - Verify dispatched events
- `assertRedirect()` - Check redirects

Best Practices:

1. Test component isolation
2. Verify state changes
3. Test event propagation
4. Validate form submission
5. Check error handling
6. Test real-time updates
7. Verify component mounting

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

Sometimes, you need to define where the browser believes it is physically on the earth. This method takes a latitude and longitude and will set the `geolocation` permission in the browser and then make the coordinates available via Javascript's getCurrentPosition API:

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

Content Assertions:

- [assertSee](#assert-see) - Assert text is visible on page
- [assertDontSee](#assert-dont-see) - Assert text is not visible on page
- [assertSeeIn](#assert-see-in) - Assert text is visible in selector
- [assertDontSeeIn](#assert-dont-see-in) - Assert text is not visible in selector
- [assertSeeAnythingIn](#assert-see-anything-in) - Assert any content in selector
- [assertSeeNothingIn](#assert-see-nothing-in) - Assert no content in selector
- [assertCount](#assert-count) - Assert element count matches

Element Assertions:

- [assertTitle](#assert-title) - Assert page title matches
- [assertTitleContains](#assert-title-contains) - Assert page title contains text
- [assertVisible](#assert-visible) - Assert element is visible
- [assertPresent](#assert-present) - Assert element exists in DOM
- [assertNotPresent](#assert-not-present) - Assert element doesn't exist
- [assertMissing](#assert-missing) - Assert element is not visible

Form Assertions:

- [assertValue](#assert-value) - Assert input value matches
- [assertValueIsNot](#assert-value-is-not) - Assert input value doesn't match
- [assertChecked](#assert-checked) - Assert checkbox is checked
- [assertNotChecked](#assert-not-checked) - Assert checkbox is not checked
- [assertIndeterminate](#assert-indeterminate) - Assert checkbox is indeterminate
- [assertRadioSelected](#assert-radio-selected) - Assert radio is selected
- [assertRadioNotSelected](#assert-radio-not-selected) - Assert radio not selected
- [assertSelected](#assert-selected) - Assert dropdown option selected
- [assertNotSelected](#assert-not-selected) - Assert option not selected
- [assertEnabled](#assert-enabled) - Assert field is enabled
- [assertDisabled](#assert-disabled) - Assert field is disabled
- [assertButtonEnabled](#assert-button-enabled) - Assert button enabled
- [assertButtonDisabled](#assert-button-disabled) - Assert button disabled

URL Assertions:

- [assertUrlIs](#assert-url-is) - Assert full URL matches
- [assertSchemeIs](#assert-scheme-is) - Assert URL scheme matches
- [assertSchemeIsNot](#assert-scheme-is-not) - Assert scheme doesn't match
- [assertHostIs](#assert-host-is) - Assert URL host matches
- [assertHostIsNot](#assert-host-is-not) - Assert host doesn't match
- [assertPortIs](#assert-port-is) - Assert URL port matches
- [assertPortIsNot](#assert-port-is-not) - Assert port doesn't match
- [assertPathIs](#assert-path-is) - Assert URL path matches
- [assertPathIsNot](#assert-path-is-not) - Assert path doesn't match
- [assertPathBeginsWith](#assert-path-begins-with) - Assert path starts with
- [assertPathEndsWith](#assert-path-ends-with) - Assert path ends with
- [assertPathContains](#assert-path-contains) - Assert path contains
- [assertQueryStringHas](#assert-query-string-has) - Assert query param exists
- [assertQueryStringMissing](#assert-query-string-missing) - Assert no query param
- [assertFragmentIs](#assert-fragment-is) - Assert URL fragment matches
- [assertFragmentBeginsWith](#assert-fragment-begins-with) - Assert fragment starts
- [assertFragmentIsNot](#assert-fragment-is-not) - Assert fragment doesn't match

Source Assertions:

- [assertScript](#assert-script) - Assert JS expression result
- [assertSourceHas](#assert-source-has) - Assert source contains code
- [assertSourceMissing](#assert-source-missing) - Assert source lacks code
- [assertSeeLink](#assert-see-link) - Assert link exists
- [assertDontSeeLink](#assert-dont-see-link) - Assert link doesn't exist

Attribute Assertions:

- [assertAttribute](#assert-attribute) - Assert attribute value matches
- [assertAttributeMissing](#assert-attribute-missing) - Assert no attribute
- [assertAttributeContains](#assert-attribute-contains) - Assert attribute contains
- [assertAttributeDoesntContain](#assert-attribute-doesnt-contain) - Assert not in attribute
- [assertAriaAttribute](#assert-aria-attribute) - Assert ARIA attribute
- [assertDataAttribute](#assert-data-attribute) - Assert data attribute

Debug Assertions:

- [assertNoSmoke](#assert-no-smoke) - Assert no console errors
- [assertNoConsoleLogs](#assert-no-console-logs) - Assert no console logs
- [assertNoJavaScriptErrors](#assert-no-javascript-errors) - Assert no JS errors
- [assertNoAccessibilityIssues](#assert-no-accessibility-issues) - Assert accessible
- [assertScreenshotMatches](#assert-screenshot-matches) - Assert visual match

</div>

### Element Interactions

<div class="collection-method-list" markdown="1">

Mouse Interactions:

- [click](#click) - Click an element
- [hover](#hover) - Hover over element
- [drag](#drag) - Drag and drop elements

Form Interactions:

- [type](#type) - Type text into input
- [typeSlowly](#type-slowly) - Type text with delay
- [select](#select) - Select dropdown option
- [append](#append) - Add text to existing input
- [clear](#clear) - Clear input field
- [radio](#radio) - Select radio button
- [check](#check) - Check checkbox
- [uncheck](#uncheck) - Uncheck checkbox
- [attach](#attach) - Upload file
- [submit](#submit) - Submit form

Button/Key Interactions:

- [press](#press) - Press a button
- [pressAndWaitFor](#press-and-wait-for) - Press and wait
- [keys](#keys) - Send keyboard input
- [withKeyDown](#withKeyDown) - Hold key while executing

Element State:

- [text](#text) - Get element text
- [attribute](#attribute) - Get element attribute
- [value](#value) - Get input value
- [withinIframe](#within-iframe) - Work with iframes

Page Control:

- [resize](#resize) - Resize browser window
- [script](#script) - Execute JavaScript
- [content](#content) - Get page content
- [url](#url) - Get current URL
- [wait](#wait) - Wait for duration
- [waitForKey](#wait-for-key) - Wait for keypress

</div>

### Debugging tests

<div class="collection-method-list" markdown="1">

Debugging Tools:

- [debug](#debug) - Enable debugging mode
- [tinker](#tinker) - Interactive debugging session

Screenshot Tools:

- [screenshot](#screenshot) - Capture page screenshot
- [screenshotElement](#screenshot-element) - Capture element screenshot

Browser Display:

- [headed](#headed) - Show browser window while testing

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

---

In this section, you have learned how to perform browser testing with Pest, enabling you to verify your application's behavior in real browsers. From form interactions to network conditions, you now have the tools to ensure your web application works correctly across different browsers and devices.

Next, let's explore how you can ensure your application's architecture remains clean and maintainable through [Arch Testing](/docs/arch-testing).
