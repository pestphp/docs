---
title: Stress Testing
description: Stress Testing is a type of testing that inspects the stability and reliability of your application under realistic or extreme conditions — depending on the scenario you setup. For example, you can use stress testing to verify that your application can handle a large number of requests or that it can handle a large amount of data.
---

# Stress Testing

Stress Testing is a type of testing that inspects the stability and reliability of your application under realistic or extreme conditions — depending on the scenario you setup. For example, you can use stress testing to verify that your application can handle a large number of requests or that it can handle a large amount of data.

<iframe width="560" height="315" src="https://www.youtube.com/embed/SaMoPZwdOCY?si=KBskkVWLUUSyK0u0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

In Pest, you can combine the power of Stress Testing with the Expectation API ensuring no stability and reliability regressions over time. This can be useful to verify that your application is stable and reliable after a new release, or after a new deployment.

Behind the scenes, this project utilizes [k6](https://k6.io/), a powerful open-source load testing tool for evaluating the performance of APIs, microservices, and websites. k6 is licensed under the **[AGPL-3.0 License](https://www.gnu.org/licenses/agpl-3.0.en.html)**, and the k6 binary is downloaded when the plugin is used for the first time.

To start using Pest's Stress Testing plugin (mostly known as Stressless), you need to require the stressless plugin via Composer.

```bash
composer require pestphp/pest-plugin-stressless --dev
```

After requiring the plugin, you may start using it in two different ways:

- Using [the `stress` command](#the-stress-command): It's useful when you want to quickly stress test a URL, without setting expectations on the result.
- Using [the `stress()` function](#the-stress-function): It's useful when you want to stress test a URL and set expectations on the result.

**Testing external domain or local IP Address?** When load testing a domain from an external network, you get a realistic picture of how your application performs under typical user loads. This includes factors like network latency and real-world internet traffic. However, when testing a local IP address within your network, the focus is on understanding the performance of your internal infrastructure in a controlled environment, without external variables like internet or DNS resolution times. It's particularly useful for identifying potential bottlenecks within your own network or server and for performance tuning of internal applications or servers. This includes tasks such as configuring PHP FPM more effectively, etc.

<a name="the-stress-command"></a>
## The Stress Command

The `stress` command is useful when you want to quickly stress test a URL, analyze the result, and all without setting expectations on the result. It's the quickest way to launch a stress test, and it happens directly on the terminal.

To get started, you may use the `stress` command and provide the URL you wish to stress test:

```bash
./vendor/bin/pest stress example.com
```

By default, the stress test duration will be `10` seconds. However, you may customize this value using the `--duration` option:

```bash
./vendor/bin/pest stress example.com --duration=5
```

In addition, the number of concurrent requests will be `1`. However, you may also customize this value using the `--concurrency` option:

```bash
./vendor/bin/pest stress example.com --concurrency=5
```

The concurrency value represents the number of concurrent requests that will be made to the given URL. For example, if you set the concurrency to `5`, Pest will **constantly make 5 concurrent requests** to the given URL until the stress test duration is reached.

You may want to be mindful of the number of concurrent requests you configure. If you configure too many concurrent requests, you may overwhelm your application, server or hit rate limits / firewalls.

Once the stress test is completed, Pest will display a summary of the stress test result.

<a name="the-stress-function"></a>
## The Stress function

Once you start understanding how stress testing works, you may want to start setting expectations on the stress test result. For example, you may want to verify that the average response time is *always* less than 100ms, and this is where the `stress()` function comes in.

To get started, simply create a regular test and use the `stress()` function to stress test a given URL:

```php
<?php

use function Pest\Stressless\stress;

it('has a fast response time', function () {
    $result = stress('example.com');

    expect($result->requests()->duration()->med())->toBeLessThan(100); // < 100.00ms
});
```

By default, the stress test duration will be 10 seconds. However, you may customize this value using the `for()->seconds()` method:

```php
$result = stress('example.com')->for(5)->seconds();
```

In addition, the number of concurrent requests will be 1. However, you may also customize this value using the `concurrently` method:

```php
$result = stress('example.com')->concurrently(requests: 2)->for(5)->seconds();
```

At any time, you may `dd` the stress test result to see its details like if you were using the `stress` command):

```php
$result = stress('example.com')->dd();
                             //->dump();
                             //->verbosely();
```


The `stress()` function return the stress test result, which you can use to set expectations. Here is the list of available methods:

<a name="the-stress-function-request-duration"></a>
### Request Duration

Returns the overall request duration in milliseconds.

```php
$result->requests()->duration()->med();
                            // ->min();
                            // ->max();
                            // ->p90();
                            // ->p95();
```

<a name="the-stress-function-requests-count"></a>
### Requests Count

Returns the number of requests made.

```php
$result->requests()->count();
```

<a name="the-stress-function-requests-rate"></a>
### Requests Rate

Returns the number of requests made per second.

```php
$result->requests()->rate();
```

<a name="the-stress-function-requests-failed-count"></a>
### Requests Failed Count

Returns the number of requests that failed.

```php
$result->requests()->failed()->count();
```

<a name="the-stress-function-requests-failed-rate"></a>
### Requests Failed Rate

Returns the number of requests that failed per second.

```php
$result->requests()->failed()->rate();
```

<a name="the-stress-function-requests-ttfb-duration"></a>
### Requests Time To First Byte Duration / TTFB

Returns the request time to first byte duration in milliseconds.

```php
$result->requests()->ttfb()->duration()->med();
                                    // ->min();
                                    // ->max();
                                    // ->p90();
                                    // ->p95();
```

<a name="the-stress-function-requests-dns-lookup-duration"></a>
### Requests DNS Lookup Duration

> This metric is affected by the network latency between the client and the DNS server.

Returns the request DNS lookup duration in milliseconds.

```php
$result->requests()->dnsLookup()->duration()->med();
                                         // ->min();
                                         // ->max();
                                         // ->p90();
                                         // ->p95();
```

<a name="the-stress-function-requests-tls-handshaking-duration"></a>
### Requests TLS Handshaking Duration

> This metric is affected by the network latency between the client and the server.

Returns the request TLS handshaking duration in milliseconds.

```php
$result->requests()->tlsHandshaking()->duration()->med();
                                              // ->min();
                                              // ->max();
                                              // ->p90();
                                              // ->p95();
```

<a name="the-stress-function-requests-download-duration"></a>
### Requests Download Duration

> This metric is affected by the network latency between the client and the server.

Returns the request download duration in milliseconds.

```php
$result->requests()->download()->duration()->med();
                                        // ->min();
                                        // ->max();
                                        // ->p90();
                                        // ->p95();
```

<a name="the-stress-function-requests-download-data-count"></a>
#### Requests Download Data Count

Returns the request download data count in bytes.

```php
$result->requests()->download()->data()->count();
```

<a name="the-stress-function-requests-download-data-rate"></a>
### Requests Download Data Rate

Returns the request download data rate in bytes per second.

```php
$result->requests()->download()->data()->rate();
```

<a name="the-stress-function-requests-upload-duration"></a>
### Requests Upload Duration

> This metric is affected by the network latency between the client and the server.

Returns the request upload duration in milliseconds.

```php
$result->requests()->upload()->duration()->med();
                                      // ->min();
                                      // ->max();
                                      // ->p90();
                                      // ->p95();
```

<a name="the-stress-function-requests-upload-data-count"></a>
### Requests Upload Data Count

Returns the request upload data count in bytes.

```php
$result->requests()->upload()->data()->count();
```

<a name="the-stress-function-requests-upload-data-rate"></a>
### Requests Upload Data Rate

Returns the request upload data rate in bytes per second.

```php
$result->requests()->upload()->data()->rate();
```

<a name="the-stress-function-test-run-count"></a>
### Test Run Concurrency

Returns the number of concurrent requests made during the stress test, which is the value you set using the `--concurrency` option or the `concurrently` method.

```php
$result->testRun()->concurrency();
```

<a name="the-stress-function-test-run-duration"></a>
### Test Run Duration

Returns the duration of the stress test, which is the value you set using the `--duration` option or the `for()->seconds()` method.

```php
$result->testRun()->duration();
```

---

Here, we've seen how to use Pest's Stress Testing plugin (aka stressless) to stress test a given URL and set expectations on the result. Moving on, let's explore how to test the coverage of your testing code: [Test Coverage](/docs/test-coverage)
