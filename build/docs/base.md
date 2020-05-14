[![Build Status](https://api.travis-ci.org/voku/Stringy.svg?branch=master)](https://travis-ci.org/voku/Stringy)
[![codecov.io](https://codecov.io/github/voku/Stringy/coverage.svg?branch=master)](https://codecov.io/github/voku/Stringy?branch=master)
[![Codacy Badge](https://api.codacy.com/project/badge/grade/97c46467e585467d884bac1130cb45e5)](https://www.codacy.com/app/voku/Stringy)
[![Latest Stable Version](https://poser.pugx.org/voku/stringy/v/stable)](https://packagist.org/packages/voku/stringy)
[![Total Downloads](https://poser.pugx.org/voku/stringy/downloads)](https://packagist.org/packages/voku/stringy) 
[![License](https://poser.pugx.org/voku/stringy/license)](https://packagist.org/packages/voku/stringy)
[![Donate to this project using Paypal](https://img.shields.io/badge/paypal-donate-yellow.svg)](https://www.paypal.me/moelleken)
[![Donate to this project using Patreon](https://img.shields.io/badge/patreon-donate-yellow.svg)](https://www.patreon.com/voku)

## :accept: Stringy 

A PHP string manipulation library with multibyte support. Compatible with PHP 7+

100% compatible with the original "[Stringy](https://github.com/danielstjules/Stringy)" library, but this fork is optimized 
for performance and is using PHP 7+ features. 

``` php
s('string')->toTitleCase()->ensureRight('y') == 'Stringy'
```

* [Why?](#why)
* [Alternative](#alternative)
* [Installation](#installation-via-composer-require)
* [OO and Chaining](#oo-and-chaining)
* [Implemented Interfaces](#implemented-interfaces)
* [PHP Class Call Creation](#php-class-call-creation)
* [Class methods](#class-methods)
    * [create](#createmixed-str--encoding-)
* [Instance methods](#instance-methods)
* [Extensions](#extensions)
* [Tests](#tests)
* [License](#license)

## Why?

In part due to a lack of multibyte support (including UTF-8) across many of
PHP's standard string functions. But also to offer an OO wrapper around the
`mbstring` module's multibyte-compatible functions. Stringy handles some quirks,
provides additional functionality, and hopefully makes strings a little easier
to work with!

```php
// Standard library
strtoupper('fòôbàř');       // 'FòôBàř'
strlen('fòôbàř');           // 10

// mbstring
mb_strtoupper('fòôbàř');    // 'FÒÔBÀŘ'
mb_strlen('fòôbàř');        // '6'

// Stringy
$stringy = Stringy\Stringy::create('fòôbàř');
$stringy->toUpperCase();    // 'FÒÔBÀŘ'
$stringy->length();         // '6'
```

## Alternative

If you like a more Functional Way to edit strings, then you can take a look at [voku/portable-utf8](https://github.com/voku/portable-utf8), also "voku/Stringy" used the functions from the "Portable UTF-8"-Class but in a more Object Oriented Way.

```php
// Portable UTF-8
use voku\helper\UTF8;
UTF8::strtoupper('fòôbàř');    // 'FÒÔBÀŘ'
UTF8::strlen('fòôbàř');        // '6'
```

## Installation via "composer require"
```shell
composer require voku/stringy
```

## Installation via composer (manually)

If you're using Composer to manage dependencies, you can include the following
in your composer.json file:

```json
"require": {
    "voku/stringy": "~6.0"
}
```

Then, after running `composer update` or `php composer.phar update`, you can
load the class using Composer's autoloading:

```php
require 'vendor/autoload.php';
```

Otherwise, you can simply require the file directly:

```php
require_once 'path/to/Stringy/src/Stringy.php';
```

And in either case, I'd suggest using an alias.

```php
use Stringy\Stringy as S;
```

## OO and Chaining

The library offers OO method chaining, as seen below:

```php
use Stringy\Stringy as S;
echo S::create('fòô     bàř')->collapseWhitespace()->swapCase(); // 'FÒÔ BÀŘ'
```

`Stringy\Stringy` has a __toString() method, which returns the current string
when the object is used in a string context, ie:
`(string) S::create('foo')  // 'foo'`

## Implemented Interfaces

`Stringy\Stringy` implements the `IteratorAggregate` interface, meaning that
`foreach` can be used with an instance of the class:

``` php
$stringy = S::create('fòôbàř');
foreach ($stringy as $char) {
    echo $char;
}
// 'fòôbàř'
```

It implements the `Countable` interface, enabling the use of `count()` to
retrieve the number of characters in the string:

``` php
$stringy = S::create('fòô');
count($stringy);  // 3
```

Furthermore, the `ArrayAccess` interface has been implemented. As a result,
`isset()` can be used to check if a character at a specific index exists. And
since `Stringy\Stringy` is immutable, any call to `offsetSet` or `offsetUnset`
will throw an exception. `offsetGet` has been implemented, however, and accepts
both positive and negative indexes. Invalid indexes result in an
`OutOfBoundsException`.

``` php
$stringy = S::create('bàř');
echo $stringy[2];     // 'ř'
echo $stringy[-2];    // 'à'
isset($stringy[-4]);  // false

$stringy[3];          // OutOfBoundsException
$stringy[2] = 'a';    // Exception
```

## PHP Class Call Creation

As of PHP 5.6+, [`use function`](https://wiki.php.net/rfc/use_function) is
available for importing functions. Stringy exposes a namespaced function,
`Stringy\create`, which emits the same behaviour as `Stringy\Stringy::create()`.

``` php
use function Stringy\create as s;

// Instead of: S::create('fòô     bàř')
s('fòô     bàř')->collapseWhitespace()->swapCase();
```

## Class methods

##### create(mixed $str [, $encoding ])

Creates a Stringy object and assigns both str and encoding properties
the supplied values. $str is cast to a string prior to assignment, and if
$encoding is not specified, it defaults to mb_internal_encoding(). It
then returns the initialized object. Throws an InvalidArgumentException
if the first argument is an array or object without a __toString method.

```php
$stringy = S::create('fòôbàř', 'UTF-8'); // 'fòôbàř'
```

If you need a collection of Stringy objects you can use the S::collection()
method. 

```php
$stringyCollection = \Stringy\collection(['fòôbàř', 'lall', 'öäü']);
```

## Instance Methods

Stringy objects are immutable. All examples below make use of PHP 5.6
function importing, and PHP 5.4 short array syntax. They also assume the
encoding returned by mb_internal_encoding() is UTF-8. For further details,
see the documentation for the create method above.

%__functions_index__%

%__functions_list__%

## Tests

From the project directory, tests can be ran using `phpunit`

## Support

For support and donations please visit [Github](https://github.com/voku/Stringy/) | [Issues](https://github.com/voku/Stringy/issues) | [PayPal](https://paypal.me/moelleken) | [Patreon](https://www.patreon.com/voku).

For status updates and release announcements please visit [Releases](https://github.com/voku/Stringy/releases) | [Twitter](https://twitter.com/suckup_de) | [Patreon](https://www.patreon.com/voku/posts).

For professional support please contact [me](https://about.me/voku).

## Thanks

- Thanks to [GitHub](https://github.com) (Microsoft) for hosting the code and a good infrastructure including Issues-Managment, etc.
- Thanks to [IntelliJ](https://www.jetbrains.com) as they make the best IDEs for PHP and they gave me an open source license for PhpStorm!
- Thanks to [Travis CI](https://travis-ci.com/) for being the most awesome, easiest continous integration tool out there!
- Thanks to [StyleCI](https://styleci.io/) for the simple but powerfull code style check.
- Thanks to [PHPStan](https://github.com/phpstan/phpstan) && [Psalm](https://github.com/vimeo/psalm) for relly great Static analysis tools and for discover bugs in the code!

## License

Released under the MIT License - see `LICENSE.txt` for details.