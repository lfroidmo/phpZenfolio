# phpZenfolio

[![Coverage Status](https://coveralls.io/repos/github/lildude/phpZenfolio/badge.svg?branch=master)](https://coveralls.io/github/lildude/phpZenfolio?branch=master) [![Build Status](https://travis-ci.org/lildude/phpZenfolio.svg)](https://travis-ci.org/lildude/phpZenfolio)

A simple object orientated wrapper for the Zenfolio API, written in PHP.

The intention of this class is to allow PHP application developers quick and easy interaction with the Zenfolio API, without having to worry about the finer details of the API.

**Note: phpZenfolio 2.0.0 and later is not backwardly compatible with earlier releases.**

## Requirements

* PHP >= 5.6.0,
* [Guzzle 6](https://github.com/guzzle/guzzle) library,
* (optional) [PHPUnit](https://phpunit.de/), [php-coveralls]() and [php-cs-fixer](http://cs.sensiolabs.org/) to run tests.

## Installation

The recommended method of installing phpZenfolio is using [Composer](http://getcomposer.org). If you have Composer installed, you can install phpZenfolio and all its dependencies from within your project directory:

```bash
$ composer require lildude/phpzenfolio
```

Alternatively, you can add the following to your project's `composer.json`:

```json
{
    "require": {
        "lildude/phpzenfolio": "^2.0"
    }
}
```

.. and then run `composer update` from within your project directory.

If you don't have Composer installed, you can download it using:

```bash
$ curl -s http://getcomposer.org/installer | php
```

## Basic Usage of the phpZenfolio Client

`phpZenfolio` follows the PSR-1, PSR-2 and PSR-4 conventions, which means you can easily use Composer's [autoloading](https://getcomposer.org/doc/01-basic-usage.md#autoloading) to integrate `phpZenfolio` into your projects.

```php
<?php
// This file is generated by Composer
require_once 'vendor/autoload.php';

$client = new phpZenfolio\Client('My Cool App/1.0 (http://app.com)'));
$photoset = $client->LoadPhotoSet(12345, 'Level1');
```

From the `$client` object, you have access to all the [Zenfolio API methods](http://www.zenfolio.com/zf/help/api/ref/methods).

## Documentation

See the [`docs` directory](docs/) or http://phpzenfolio.com/docs/ for more detailed documentation.

## Examples

phpZenfolio comes with four examples to help get you on your way.

* [`example-popular.php`](examples/example-popular.php) illustrates how to obtain the 96 most popular galleries and display their title image linking to each individual gallery.
* [`example-login.php`](examples/example-login.php) illustrates how to login and display the images in your first photoset or collection.
* [`example-user.php`](examples/example-user.php) illustrates how to display the first 96 public photos of the specified user's first public photoset found.
* [`example-create-photoset.php`](examples/example-create-photoset.php) illustrates how to create a new gallery photoset in the authenticated user's root photoset group, and upload an image to this gallery.

## Need Help or Have Questions?

The best way to get help with implementing phpZenfolio into your projects is to open an [issue](https://github.com/lildude/phpZenfolio/issues).  This allows you to easily search for other issues where others may have asked to the same questions or hit the same problems and if they haven't, your issue will add to the resources available to others at a later date.

Please don't be shy. If you've got a question, problem or are just curious about something, there's a very good chance someone else is too, so go ahead and open an issue and ask.

## Contributing

Found a bug or want to make phpZenfolio even better? Please feel free to open a pull request with your changes, but be sure to check out the [CONTRIBUTING.md](CONTRIBUTING.md) first for some tips and guidelines. No pull request is too small.

## Changes

All notable changes to this project are documented in [CHANGELOG.md](CHANGELOG.md).

## License

All code is licensed under the [MIT License](https://opensource.org/licenses/MIT) and all documentation is licensed under the [CC BY 4.0 license](https://creativecommons.org/licenses/by/4.0/).
