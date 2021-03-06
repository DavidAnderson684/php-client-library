Copy PHP API
==================

[![Latest Stable Version](https://poser.pugx.org/barracuda/copy/version.png)](https://packagist.org/packages/barracuda/copy) [![Composer Downloads](https://poser.pugx.org/barracuda/copy/d/total.png)](https://packagist.org/packages/barracuda/copy) [![Build Status](https://secure.travis-ci.org/copy-app/php-client-library.png?branch=master)](http://travis-ci.org/copy-app/php-client-library)

A php library for communicating with the copy cloud api

This library demos the binary part api, an efficient way to de-dupe and send/receive data with the Copy cloud, and the JSONRPC api used by the Copy agent, and Copy mobile devices, for doing advanced things with Copy.

This demo works with the OAUTH api, which you will need to setup at the Copy developer portal (https://www.copy.com/developer/).

### The Basics

## Connect to the cloud

```php
// create a cloud api connection to Copy
$copy = new \Barracuda\Copy\API($consumerKey, $consumerSecret, $accessToken, $tokenSecret);
```

## List items

```php
// list items in the root
$items = $copy->listPath('/');
```

## Uploading a file

```php
// open a file to upload
$fh = fopen('/tmp/file', 'rb');

// upload the file in 1MB chunks
$parts = array();
while ($data = fread($fh, 1024 * 1024)) {
    $part = $copy->sendData($data);
    array_push($parts, $part);
}

// close the file
fclose($fh);

// finalize the file
$copy->createFile('/copy-file-path', $parts);
```

## Downloading a file
```php
// obtain a list of file and parts
$files = $copy->listPath('/copy-file-path', array("include_parts" => true));

// process each file
foreach ($files as $file) {
	$data = '';

	// enumerate the parts in the latest revision
    foreach ($file->revisions[0]->parts as $part) {
        $data .= $copy->getPart($part->fingerprint, $part->size);
    }
}
```

## Delete a file
```php
$copy->removeFile('/copy-file-path');
```

## Rename an object
```php
$copy->rename('/source-copy-file-path', '/destination-copy-file-path');
```

### Installing via Composer

The recommended way to install the Copy PHP API is through [Composer](http://getcomposer.org).

```bash
# Install Composer
curl -sS https://getcomposer.org/installer | php

# Add Copy API as a dependency
php composer.phar require barracuda/copy
```

After installing, you need to require Composer's autoloader:

```php
require 'vendor/autoload.php';
```
### Running the tests

First install the dependencies for the library using Composer. See above for how to install Composer.

```bash
composer install
```

Then add connection info for your Copy account as environment variables:

```bash
export CONSUMER_KEY=<check the developer portal>
export CONSUMER_SECRET=<check the developer portal>
export ACCESS_TOKEN=<OAuth token>
export ACCESS_TOKEN_SECRET=<OAuth secret>
```

### License

This library is MIT licensed, so feel free to use it anyway you see fit.
