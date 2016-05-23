phpZenfolio
===========

phpZenfolio is a simple object orientated wrapper for the Zenfolio API, written in PHP.


## Requirements

* PHP >= 5.6.0,
* [Guzzle 6](https://github.com/guzzle/guzzle) library and the [Guzzle OAuth1 Subscriber](https://github.com/guzzle/oauth-subscriber),
* (optional) [PHPUnit](https://phpunit.de/) and [php-cs-fixer](http://cs.sensiolabs.org/) to run tests.

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

## Usage

In order to use phpZenfolio, you will need to instantiate a new instance of the phpZenfolio object and then call the methods you need.  phpZenfolio implements all methods and arguments as documented in the Zenfolio API. Accordingly, when calling a Zenfolio method, you will need to ensure that you pass the parameters exactly as they are documented in the [Zenfolio API documentation](http://http://www.zenfolio.com/zf/help/api).

Remember: *ALL* function names and arguments *ARE* case sensitive and the order is important.

To make things easy for developers, phpZenfolio also provides several of it's own methods.  These methods are: `login()`, `enableCache()`, `clearCache()`, `upload()`, `setProxy()`, `imageUrl()` and `setAdapter()`.  All phpZenfolio methods, and its constructor, take their arguments either as an associative array or as a list of `param=value` strings, unless otherwise documented. These methods are documented in more detail later in this document.

To use phpZenfolio, all you have to do is include the file in your PHP scripts and create an instance.  For example:

```
require_once("phpZenfolio/phpZenfolio.php");
$f = new phpZenfolio(... arguments go here ...);
```

The constructor takes two arguments, one obligatory and one optional:

* `AppName` - Required

  This is the name, version and URL of the application you have built using phpZenfolio. There is no required format, but something like:

  ```
  "My Cool App/1.0 (http://my.url.com)"
  ```

  ... would be very useful and will allow Zenfolio to identify your application in the event of a problem.

* `APIVer` - Optional  
  Default: 1.8

  Use this to set the endpoint you wish to use.  The default is 1.8 as this is the latest endpoint provided by Zenfolio.

As the constructor is a phpZenfolio specific method, it can be instantiated using one of the following methods:

* Arguments as strings:

  ```
  $f = new phpZenfolio("AppName=My Cool App/1.0 (http://app.com)", "APIVer=1.8");
  ```

* Arguments as an associative array:

  ```
  $f = new phpZenfolio(array(
      "AppName" => "My Cool App/1.0 (http://app.com)",
      "APIVer" => "1.8")
  );
  ```

Naturally, you can predefine the array before instantiating the object and just pass the array variable.

With the instance instantiated, you can now interact with the Zenfolio API using Zenfolio's native methods exactly as they're documented. Arguments to all Zenfolio native methods must be provided in the order they are documented in the API documentation.  For example, use the following to get all recent sets that are of the PhotoSetType 'Gallery':

  ```
  $f->GetRecentSets('Gallery', 0, 3);
  ```

Note the method's capitalisation and the arguments, these are as they are documented in the [`GetRecentSets()`](http://www.zenfolio.com/zf/help/api/ref/methods/getrecentsets) method documentation.

Some of the Zenfolio API methods, like `CreatePhotoSet()`, require an object to be passed as one of the arguments. The object can be passed either as an associative array:

  ```
  $photoSetUpdater = array(
      "Title" => "PhotoSet Title",
      "Caption" => "PhotoSet Caption via API",
      "Keywords" => array("Keyword1", "keyword2"),
      "Categories" => array(),
      "CustomReference" => "testing/test-photoset"
  );
  $f->CreatePhotoSet(12345, 'Gallery', $photoSetUpdater);
  ```

... or as a standard class object:

  ```
  $photoSetUpdater = new stdClass();
  $photoSetUpdater->Title = "PhotoSet Title";
  $photoSetUpdater->Caption = "PhotoSet Caption via Object";
  $photoSetUpdater->Keywords = array("Keyword1", "keyword2");
  $photoSetUpdater->Categories = array();
  $photoSetUpdater->CustomReference = "testing/test-photoset";
  $f->CreatePhotoSet(12345, 'Gallery', $photoSetUpdater);
  ```

All data returned by the method call is returned as the API documents it with the exception being objects are actually returned as arrays by phpZenfolio.  In the event of an error, phpZenfolio will throw one of two exceptions: `PhpZenfolioException` in the event of a problem detected by phpZenfolio or `HttpRequestException` in the event of a problem detected by the code used to communicate with the API.  Your application will need to catch these exceptions.


## Authentication

Many of the Zenfolio API methods are open to all users, whether they have Zenfolio accounts or not, however anything private or related to modification requires authentication.

The Zenfolio API provides [two methods of authentication](http://www.zenfolio.com/zf/help/api/guide/auth): Plain-Text and Challenge-Response.  Both are equally good with the slightly more secure method being the Challenge-Response method as your password never travels across the wire.

phpZenfolio allows you to use the API methods as documented, however to make things easy, a single `login()` method exists to allow you to authentication using either of these authentication methods:

* Challenge-Response (default):

  ```
  $f->login("Username=<username>", "Password=<password>");
  ```

* Plain-Text:

  ```
  $f->login("Username=<username>", "Password=<password>", "Plaintext=TRUE");
  ```

The Plain-Text method uses HTTPS/SSL for the authentication step to ensure your username and password are encrypted when transmitted to Zenfolio.


## Caching

Caching can be very important to a project as it can drastically improve the performance of your application.

phpZenfolio has caching functionality that can cache data to a database or files, you just need to enable it.

It is recommended that caching is enabled immediately after a new phpZenfolio instance is created, and before any other phpZenfolio methods are called.

To enable caching, use the `enableCache()` function.

The `enableCache()` method takes 4 arguments:

* `type` - Required

  This is "db" for database or "fs" for filesystem.

* `dsn` - Required for `type=db`

  This a PEAR::MDB2 DSN connection string, for example:

  ```
  mysql://user:password@server/database
  ```

phpZenfolio uses the MDB2 PEAR module to interact with the database if you use database based caching.  phpZenfolio does *NOT* supply the necessary PEAR modules.  If you with to use a database for caching, you will need to download and install PEAR, the MDB2 PEAR module and the corresponding database driver yourself.  See [MDB2 Manual](http://pear.php.net/manual/en/package.database.mdb2.intro.php) for details.

* `cache_dir` - Required for `type=fs`

  This is the folder/directory that the web server has write access to. This directory must already exist.

Use absolute paths for best results as relative paths may have unexpected behaviour. They'll usually work, you'll just want to test them.

You may not want to allow the world to view the files that are created during caching.  If you want to hide this information, either make sure that your permissions are set correctly, or prevent the webserver from displaying `*.cache` files.

In Apache, you can specify this in the configuration files or in a .htaccess file with the following directives:

```
<FilesMatch "\.cache$">
    Deny from all
</FilesMatch>
```

Alternatively, you can specify a directory that is outside of the web server's document root.

* `cache_expire` - Optional  
  Default: 3600

  This is the maximum age of each cache entry in seconds.

* `table` - Optional  
  Default: `phpzenfolio_cache`

  This is the database table name that will be used for storing the cached data.  This is only applicable for database (db) caching and will be ignored if included for filesystem (fs) caching.

  If the table does not exist, phpZenfolio will attempt to create it.

Each of the caching methods can be enabled as follows:

* Filesystem based cache:

  ```
  $f->enableCache("type=fs", "cache_dir=/tmp", "cache_expire=86400");
  ```

* Database based cache:

  ```
  $f->enableCache("type=db", "dsn=mysql://USERNAME:PASSWORD_database", "cache_expire=86400");
  ```

If you have caching enabled, and you make changes, it's a good idea to call `clearCache()` to refresh the cache so your changes are reflected immediately.


## Uploading

Uploading is very easy.  You can either upload an image from your local system using the phpZenfolio supplied `upload()` method, or from a location on the web using the API's `CreatePhotoFromUrl()` method.

* Upload Local File:

  To upload from your local filesystem using the phpZenfolio `upload()` method, you will need to have logged into Zenfolio via the API and have the `PhotoSetId` or it's `UploadUrl` as returned by the API.

  Then it's just a matter of calling the method with the various optional parameters.

  * Upload using the PhotoSetId:

    ```
    $f->upload("PhotoSetId=123456", "File=/path/to/image.jpg");
    ```

  * Upload using the UploadUrl:

    ```
    $f->upload("UploadUrl=http://up.zenfolio.com/....",
        "File=/path/to/image.jpg");
    ```

  At this time, the only supported options you can pass at the time of uploading are a `filename` the `modified` parameter which takes a RFC2822 formatted date string...

    ```
    $f->upload("PhotoSetId=123456",
        "File=/path/to/image.jpg",
        "filename=newfilename.jpg",
        "modified=Thu, 14 Jan 2010 13:08:07 +0200");
    ```

  If you don't specify a filename, the original filename is used.


* Upload from the web:

  Uploading to Zenfolio using a URL is done purely by the Zenfolio `CreatePhotoFromUrl()` API method:

  ```
  $f->CreatePhotoFromUrl(12344, "http://www.example.com/images/image.jpg", null);
  ```

  You can find full details on the options this method accepts in the [CreatePhotoFromUrl](http://www.zenfolio.com/zf/help/api/ref/methods/createphotofromurl) method documentation.

  Unfortunately, there is no way to pass things like the photo title etc at the time of upload. You will need to set these later using the `UpdatePhoto()` method.


## Replacing Photos

In order to replace a photo, you will need to upload a new photo and then replace the old photo with the new using the Zenfolio [`ReplacePhoto()`](http://www.zenfolio.com/zf/help/api/ref/methods/replacephoto) API method.



## Other Notes

* By default, phpZenfolio will attempt to use Curl to communicate with the
  Zenfolio API endpoint if it's available.  If not, it'll revert to using
  sockets based communication using `fsockopen()`.  If you wish to force the
  use of sockets, you can do so using the phpZenfolio supplied
  `setAdapter()` right after instantiating your instance:

  ```
  $f = new phpZenfolio("AppName=<value>");
  $f->setAdapter("socket");
  ```

  Valid arguments are "curl" (default) and "socket".

* phpZenfolio implements [Zenfolio objects](http://www.zenfolio.com/zf/help/api/ref/objects) as arrays.  This makes implementation and usage easier.

* Some people will need to use phpZenfolio from behind a proxy server.  You can use the `setProxy()` method to set the appropriate proxy settings.

  For example:

  ```
  $f = new phpZenfolio("AppName=<value>");
  $f->setProxy("server=<proxy_server>", "port=<value>");
  ```

  All your calls will then pass through the specified proxy on the specified port.

  If your proxy server requires a username and password, then add those options to the `setProxy()` method arguments too.

  For example:

  ```
  $f = new phpZenfolio("AppName=<value>");
  $f->setProxy("server=<proxy_server>",
      "port=<value>",
      "user=<proxy_username>",
      "password=<proxy_password>");
  ```

  Note: Proxy support is currently only available when using the default "curl" adapter.

* To make it easy to obtain the direct URL to an image, phpZenfolio supplies a `imageURL()` method that takes the Photo object as returned by methods like `LoadPhoto()` and `LoadPhotoSetPhotos()` and an integer for the desired photo size where the integer is one of those documented at http://www.zenfolio.com/zf/help/api/guide/download .

  For example:

  ```
  $f = new phpZenfolio("AppName=<value>");
  $photos = $f->LoadPhotoSetPhotos(<photosetID>, <startingIndex>, <numberOfPhotos>);
  foreach ($photos as $photo) {
      echo '<img src="',phpZenfolio::imageUrl($photo, 1),'" />';
  }
  ```

* If phpZenfolio encounters an error, or Zenfolio returns a "failure" response, an exception will be thrown and your application will stop executing. If there is a problem with communicating with the endpoint, a HttpRequestException will be thrown.  If an error is detected elsewhere, a PhpZenfolioException will be thrown.

  It is recommended that you configure your application to catch exceptions from phpZenfolio.


## Examples

phpZenfolio comes with 3 examples to help get you on your way.

* `example-popular.php` illustrates how to obtain the 100 most popular galleries and display their title image linking to each individual gallery.

* `example-login.php` illustrates how to login and display the images in your first photoset or collection.

* `example-user.php` illustrates how to display the first 100 public photos of the user's the first public photoset found.

And that's all folks.

Keep up to date on developments and enhancements to phpZenfolio at
[phpzenfolio.com](http://phpzenfolio.com/).

If you encounter any problems with phpZenfolio, please check the list of known
issues with phpZenfolio in the GitHub repository at https://github.com/lildude/phpZenfolio/issues .
If your issue is not there, please feel free to open a new issue and provide as many details as you can.

This document is also available online at [phpzenfolio.com/docs](http://phpzenfolio.com/docs).
