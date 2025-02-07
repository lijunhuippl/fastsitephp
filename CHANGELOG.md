# FastSitePHP Change Log

FastSitePHP uses [Semantic Versioning](https://docs.npmjs.com/about-semantic-versioning). This change log includes Framework release history and new website features or major changes.

## 1.2.2 (February 26, 2020)

* Update `I18N::langFile()` for a minor edge case bug related to 404 redirect on missing language. It was found to affect local development with the PHP built-in server when a single `index.php` file is used for routing.

## 1.2.1 (January 27, 2020)

* Improved Framework support with FreeBSD
  * `FileEncryption` Class now has improved support for large file encryption (2+ GB) on a basic FreeBSD Server Setup
  * Additional documentation on FreeBSD Server Setup: https://www.fastsitephp.com/en/documents/install-php-on-linux
* Fixed a bug with `FastSitePHP\Encoding\Json::encode()` that prevented it from working when using PHP `5.3`. This did not affect any other version of PHP.

* **Thanks Nicolas CARPi for opening the issue related to the following items** https://github.com/NicolasCARPi
* Adding support for PHP linting with https://github.com/phpstan/phpstan
  * For info on how to run `phpstan` see comments in file: https://github.com/fastsitephp/fastsitephp/blob/master/phpstan.neon
* Updated `README.md` file with warning about using older versions of PHP. Currently FastSitePHP supports older versions of PHP that are widely used by not considered secure.
* Updated `README.md` with a brief description of how Unit Testing works.

## 1.2.0 (January 10, 2020)

* The core `Application` object now handles route `filter` functions that return a `Response` object instead of a `bool`. This allows for easier unit testing of custom middleware. See code example below.
* Added function `Request->bearerToken()`
* Added function `I18N::hasLang($lang)`
* Updated function `I18N::getUserDefaultLang()` to validate the language from malicious user attempts to attack a site from the 'Accept-Language' request header. This is simply an additional safety check as the key validation is handled by `Security::dirContainsFile` in the function.

~~~php
// Example route
$app->get('/:lang/auth-demo', 'AuthDemo')->filter('Auth.hasAccess');

// Prior to this change an Auth Middleware Object would have likely called [exit()]
class Auth
{
    public function hasAccess(Application $app)
    {
        $res = new Response($app)
        $res
            ->statusCode(401)
            ->header('WWW-Authenticate', 'Bearer')
            ->json(['success' => false, 'authRequired' => true])
            ->send();
        exit();
    }
}

// Now the Middleware Object can return a Response Object.
// This allows for easier CLI testing of an Apps Middleware.
class Auth
{
    public function hasAccess(Application $app)
    {
        return (new Response($app))
            ->statusCode(401)
            ->header('WWW-Authenticate', 'Bearer')
            ->json(['success' => false, 'authRequired' => true]);
    }
}
~~~

## 1.1.3 (December 24, 2019)

* Updated `Application->rootUrl()` and `AppMin->rootUrl()` for edge case error when using built-in PHP Server
  * Error did not affect Apache, nginx, IIS, or most PHP built-in server setups
  * When PHP built-in server with fallback 'php -S localhost:3000 website/public/index.php' and code similar to the example below the a site would redirect with 2 forward slashes (example: `http://localhost:3000//en/`).
  * The previous work-around was to use `$app->redirect('/' . I18N::getUserDefaultLang() . '/');`
  * The below code now works correctly in all tested environments

~~~php
$app->get('/', function() use ($app) {
    $app->redirect($app->rootUrl() . I18N::getUserDefaultLang() . '/');
});
~~~

## Website (December 24, 2019)

* Spanish `es` translations complete for all JSON files on the main site
  * https://fastsitephp.com/es/
  * **Thanks Tibaldo Pirela Reyes** for helping with translations! https://github.com/tpirelar

## 1.1.2 (December 16, 2019)

* Updates for easier nginx suppport using a basic nginx install
  * Change affected `Application->requestedPath()` and `AppMin->requestedPath()` so handle empty string "" for PATH_INFO

## Website (December 12, 2019)

* Created a script that allows easy web server setup with Apache, PHP, and the FastSitePHP Starter Site

~~~bash
wget https://www.fastsitephp.com/downloads/create-fast-site.sh
sudo bash create-fast-site.sh
~~~

## 1.1.1 (December 12, 2019)

* Brazilian Portuguese `pt-BR` language support added for `L10N` - formatting dates, times, and numbers
  * https://www.fastsitephp.com/en/api/Lang_L10N
  * Previously `pt-BR` would have fallen back to `pt`
  * **Thanks Marcelo dos Santos Mafra** for finding and providing this! https://github.com/msmafra
* Class `Lang\L10N`
  * https://www.fastsitephp.com/en/api/Lang_L10N
  * Fixed bug with `Lang\L10N` class so that a 404 page is correctly sent by default
  * **Thanks eGirlAsm** for finding the bug! https://github.com/eGirlAsm
  * Added link updates for unicode-cldr in the header docs
* Changed default 404 page title message from 'Page Not Found' to '404 - Page Not Found' for clarity - Property [$app->not_found_page_title]
  * https://www.fastsitephp.com/en/api/Application

## 1.1.0 (December 10, 2019)

* New Class `FastSitePHP\FileSystem\Sync`
* Class `FastSitePHP\Lang\I18N` 
  * Added new static function: `I18N::getUserDefaultLang()`
  * Fixed edge case error when multple calls are made to `I18N::langFile()` and a file is missing after the first call.

## 1.0.0 (November 14, 2019)

* Initial public release
