# H&H Crawler

A fork of spatie/crawler v2 with some adjustments. Only used for an internal project.

# Crawl links on a website

[![Latest Version on Packagist](https://img.shields.io/packagist/v/spatie/crawler.svg?style=flat-square)](https://packagist.org/packages/spatie/crawler)
[![Software License](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](LICENSE.md)
[![Build Status](https://img.shields.io/travis/spatie/crawler/master.svg?style=flat-square)](https://travis-ci.org/spatie/crawler)
[![Quality Score](https://img.shields.io/scrutinizer/g/spatie/crawler.svg?style=flat-square)](https://scrutinizer-ci.com/g/spatie/crawler)
[![StyleCI](https://styleci.io/repos/45406338/shield)](https://styleci.io/repos/45406338)
[![Total Downloads](https://img.shields.io/packagist/dt/spatie/crawler.svg?style=flat-square)](https://packagist.org/packages/spatie/crawler)

This package provides a class to crawl links on a website. Under the hood Guzzle promises are used to [crawl multiple urls concurrently](http://docs.guzzlephp.org/en/latest/quickstart.html?highlight=pool#concurrent-requests).

Because the crawler can execute JavaScript, it can crawl JavaScript rendered site. Under the hood [headless Chrome](https://github.com/spatie/browsershot) is used to power this feature.

Spatie is a webdesign agency in Antwerp, Belgium. You'll find an overview of all our open source projects [on our website](https://spatie.be/opensource).

## Installation

This package can be installed via Composer:

``` bash
composer require spatie/crawler
```

## Usage

The crawler can be instantiated like this

```php
Crawler::create()
    ->setCrawlObserver(<implementation of \Spatie\Crawler\CrawlObserver>)
    ->startCrawling($url);
```

The argument passed to `setCrawlObserver` must be an object that implements the `\Spatie\Crawler\CrawlObserver` interface:

```php
/**
 * Called when the crawler will crawl the given url.
 *
 * @param \Spatie\Crawler\Url $url
 */
public function willCrawl(Url $url);

/**
 * Called when the crawler has crawled the given url.
 *
 * @param \Spatie\Crawler\Url $url
 * @param \Psr\Http\Message\ResponseInterface $response
 * @param \Spatie\Crawler\Url $foundOn
 */
public function hasBeenCrawled(Url $url, $response, Url $foundOn = null);

/**
 * Called when the crawl has ended.
 */
public function finishedCrawling();
``` 

### Executing JavaScript

By default the crawler will not execute JavaScript. This is how you can enable the execution of JavaScript:

```php
Crawler::create()
    ->executeJavaScript()
    ...
```

Under the hood [headless Chrome](https://github.com/spatie/browsershot) is used to execute JavaScript. Here are some pointers on [how to install it on your system](https://github.com/spatie/browsershot#requirements).

The package will make an educated guess as to where Chrome is installed on your system. You can also manually pass the location of the Chrome binary to  `executeJavaScript()`

```php
Crawler::create()
    ->executeJavaScript($pathToChrome)
    ...
```

### Filtering certain urls

You can tell the crawler not to visit certain urls by passing using the `setCrawlProfile`-function. That function expects
an objects that implements the `Spatie\Crawler\CrawlProfile`-interface:

```php
/*
 * Determine if the given url should be crawled.
 */
public function shouldCrawl(Url $url): bool;
```

This package comes with three `CrawlProfiles` out of the box:

- `CrawlAllUrls`: this profile will crawl all urls on all pages including urls to an external site. 
- `CrawlInternalUrls`: this profile will only crawl the internal urls on the pages of a host.
- `CrawlSubdomainUrls`: this profile will only crawl the internal urls and its subdomains on the pages of a host.

## Setting the number of concurrent requests

To improve the speed of the crawl the package concurrently crawls 10 urls by default. If you want to change that number you can use the `setConcurrency` method.

```php
Crawler::create()
    ->setConcurrency(1) //now all urls will be crawled one by one
```

## Setting the maximum crawl count

By default, the crawler continues until it has crawled every page of the supplied URL. If you want to limit the amount of urls the crawler should crawl you can use the `setMaximumCrawlCount` method.

```php
// stop crawling after 5 urls

Crawler::create()
    ->setMaximumCrawlCount(5) 
```

## Setting the maximum crawl depth

By default, the crawler continues until it has crawled every page of the supplied URL. If you want to limit the depth of the crawler you can use the `setMaximumDepth` method.

```php
Crawler::create()
    ->setMaximumDepth(2) 
```

## Using a custom crawl queue 

When crawling a site the crawler will put urls to be crawled in a queue. By default this queue is stored in memory using the built in `CollectionCrawlQueue`. 

When a site is very large you may want to store that queue elsewhere, maybe a database. In such cases you can write your own crawl queue. 

A valid crawl queue is any class that implements the `Spatie\Crawler\CrawlQueue\CrawlQueue`-interface. You can pass your custom crawl queue via the `setCrawlQueue` method on the crawler. 

```php
Crawler::create()
    ->setCrawlQueue(<implementation of \Spatie\Crawler\CrawlQueue\CrawlQueue>) 
```
    
## Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information what has changed recently.

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

## Testing

To run the tests you'll have to start the included node based server first in a separate terminal window.

```bash
cd tests/server
npm install
./start_server.sh
```

With the server running, you can start testing.
```bash
vendor/bin/phpunit
```

## Security

If you discover any security related issues, please email freek@spatie.be instead of using the issue tracker.

## Postcardware

You're free to use this package, but if it makes it to your production environment we highly appreciate you sending us a postcard from your hometown, mentioning which of our package(s) you are using.

Our address is: Spatie, Samberstraat 69D, 2060 Antwerp, Belgium.

We publish all received postcards [on our company website](https://spatie.be/en/opensource/postcards).

## Credits

- [Freek Van der Herten](https://github.com/freekmurze)
- [All Contributors](../../contributors)

## Support us

Spatie is a webdesign agency based in Antwerp, Belgium. You'll find an overview of all our open source projects [on our website](https://spatie.be/opensource).

Does your business depend on our contributions? Reach out and support us on [Patreon](https://www.patreon.com/spatie). 
All pledges will be dedicated to allocating workforce on maintenance and new awesome stuff.

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
