![](https://github.com/googleapis/google-api-php-client/workflows/.github/workflows/tests.yml/badge.svg)

# Google APIs Client Library for PHP #

<dl>
  <dt>Reference Docs</dt><dd><a href="https://googleapis.github.io/google-api-php-client/main/">https://googleapis.github.io/google-api-php-client/main/</a></dd>
  <dt>License</dt><dd>Apache 2.0</dd>
</dl>

The Google API Client Library enables you to work with Google APIs such as Gmail, Drive or YouTube on your server.

These client libraries are officially supported by Google.  However, the libraries are considered complete and are in maintenance mode. This means that we will address critical bugs and security issues but will not add any new features.

## Google Cloud Platform

For Google Cloud Platform APIs such as [Datastore][cloud-datastore], [Cloud Storage][cloud-storage], [Pub/Sub][cloud-pubsub], and [Compute Engine][cloud-compute], we recommend using the Google Cloud client libraries. For a complete list of supported Google Cloud client libraries, see [googleapis/google-cloud-php](https://github.com/googleapis/google-cloud-php).

[cloud-datastore]: https://github.com/googleapis/google-cloud-php-datastore
[cloud-pubsub]: https://github.com/googleapis/google-cloud-php-pubsub
[cloud-storage]: https://github.com/googleapis/google-cloud-php-storage
[cloud-compute]: https://github.com/googleapis/google-cloud-php-compute

## Requirements ##
* [PHP 5.6.0 or higher](https://www.php.net/)

## Installation ##

You can use **Composer** or simply **Download the Release**


### Download the Release

If you prefer not to use composer, you can download the package in its entirety. The [Releases](https://github.com/googleapis/google-api-php-client/releases) page lists all stable versions. Download any file
with the name `google-api-php-client-[RELEASE_NAME].zip` for a package including this library and its dependencies.

Uncompress the zip file you download, and include the autoloader in your project:

```php
require_once '/path/to/google-api-php-client/vendor/autoload.php';
```

For additional installation and setup instructions, see [the documentation](docs/).

## Examples ##
See the [`examples/`](examples) directory for examples of the key client features. You can
view them in your browser by running the php built-in web server.

```
$ php -S localhost:8000 -t examples/
```

And then browsing to the host and port you specified
(in the above example, `http://localhost:8000`).

### Basic Example ###

```php
// include your composer dependencies
require_once 'vendor/autoload.php';

$client = new Google\Client();
$client->setApplicationName("Client_Library_Examples");
$client->setDeveloperKey("YOUR_APP_KEY");

$service = new Google\Service\Books($client);
$query = 'Henry David Thoreau';
$optParams = [
  'filter' => 'free-ebooks',
];
$results = $service->volumes->listVolumes($query, $optParams);

foreach ($results->getItems() as $item) {
  echo $item['volumeInfo']['title'], "<br /> \n";
}
```

### Authentication with OAuth ###

> An example of this can be seen in [`examples/simple-file-upload.php`](examples/simple-file-upload.php).

1. Follow the instructions to [Create Web Application Credentials](docs/oauth-web.md#create-authorization-credentials)
1. Download the JSON credentials
1. Set the path to these credentials using `Google\Client::setAuthConfig`:

    ```php
    $client = new Google\Client();
    $client->setAuthConfig('/path/to/client_credentials.json');
    ```

1. Set the scopes required for the API you are going to call

    ```php
    $client->addScope(Google\Service\Drive::DRIVE);
    ```

1. Set your application's redirect URI

    ```php
    // Your redirect URI can be any registered URI, but in this example
    // we redirect back to this same page
    $redirect_uri = 'http://' . $_SERVER['HTTP_HOST'] . $_SERVER['PHP_SELF'];
    $client->setRedirectUri($redirect_uri);
    ```

1. In the script handling the redirect URI, exchange the authorization code for an access token:

    ```php
    if (isset($_GET['code'])) {
        $token = $client->fetchAccessTokenWithAuthCode($_GET['code']);
    }
    ```

### Authentication with Service Accounts ###

> An example of this can be seen in [`examples/service-account.php`](examples/service-account.php).

Some APIs
(such as the [YouTube Data API](https://developers.google.com/youtube/v3/)) do
not support service accounts. Check with the specific API documentation if API
calls return unexpected 401 or 403 errors.

1. Follow the instructions to [Create a Service Account](docs/oauth-server.md#creating-a-service-account)
1. Download the JSON credentials
1. Set the path to these credentials using the `GOOGLE_APPLICATION_CREDENTIALS` environment variable:

    ```php
    putenv('GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account.json');
    ```

1. Tell the Google client to use your service account credentials to authenticate:

    ```php
    $client = new Google\Client();
    $client->useApplicationDefaultCredentials();
    ```

1. Set the scopes required for the API you are going to call

    ```php
    $client->addScope(Google\Service\Drive::DRIVE);
    ```

1. If you have delegated domain-wide access to the service account and you want to impersonate a user account, specify the email address of the user account using the method setSubject:

    ```php
    $client->setSubject($user_to_impersonate);
    ```

#### How to use a specific JSON key

If you want to a specific JSON key instead of using `GOOGLE_APPLICATION_CREDENTIALS` environment variable, you can do this:

```php
$jsonKey = [
   'type' => 'service_account',
   // ...
];
$client = new Google\Client();
$client->setAuthConfig($jsonKey);
```

### Making Requests ###

The classes used to call the API in [google-api-php-client-services](https://github.com/googleapis/google-api-php-client-services) are autogenerated. They map directly to the JSON requests and responses found in the [APIs Explorer](https://developers.google.com/apis-explorer/#p/).

A JSON request to the [Datastore API](https://developers.google.com/apis-explorer/#p/datastore/v1beta3/datastore.projects.runQuery) would look like this:

```json
POST https://datastore.googleapis.com/v1beta3/projects/YOUR_PROJECT_ID:runQuery?key=YOUR_API_KEY

{
    "query": {
        "kind": [{
            "name": "Book"
        }],
        "order": [{
            "property": {
                "name": "title"
            },
            "direction": "descending"
        }],
        "limit": 10
    }
}
```

Using this library, the same call would look something like this:

```php
// create the datastore service class
$datastore = new Google\Service\Datastore($client);

// build the query - this maps directly to the JSON
$query = new Google\Service\Datastore\Query([
    'kind' => [
        [
            'name' => 'Book',
        ],
    ],
    'order' => [
        'property' => [
            'name' => 'title',
        ],
        'direction' => 'descending',
    ],
    'limit' => 10,
]);

// build the request and response
$request = new Google\Service\Datastore\RunQueryRequest(['query' => $query]);
$response = $datastore->projects->runQuery('YOUR_DATASET_ID', $request);
```

However, as each property of the JSON API has a corresponding generated class, the above code could also be written like this:

```php
// create the datastore service class
$datastore = new Google\Service\Datastore($client);

// build the query
$request = new Google\Service\Datastore_RunQueryRequest();
$query = new Google\Service\Datastore\Query();
//   - set the order
$order = new Google\Service\Datastore_PropertyOrder();
$order->setDirection('descending');
$property = new Google\Service\Datastore\PropertyReference();
$property->setName('title');
$order->setProperty($property);
$query->setOrder([$order]);
//   - set the kinds
$kind = new Google\Service\Datastore\KindExpression();
$kind->setName('Book');
$query->setKinds([$kind]);
//   - set the limit
$query->setLimit(10);

// add the query to the request and make the request
$request->setQuery($query);
$response = $datastore->projects->runQuery('YOUR_DATASET_ID', $request);
```

The method used is a matter of preference, but *it will be very difficult to use this library without first understanding the JSON syntax for the API*, so it is recommended to look at the [APIs Explorer](https://developers.google.com/apis-explorer/#p/) before using any of the services here.

### Making HTTP Requests Directly ###

If Google Authentication is desired for external applications, or a Google API is not available yet in this library, HTTP requests can be made directly.

If you are installing this client only to authenticate your own HTTP client requests, you should use [`google/auth`](https://github.com/googleapis/google-auth-library-php#call-the-apis) instead.

The `authorize` method returns an authorized [Guzzle Client](http://docs.guzzlephp.org/), so any request made using the client will contain the corresponding authorization.

```php
// create the Google client
$client = new Google\Client();

/**
 * Set your method for authentication. Depending on the API, This could be
 * directly with an access token, API key, or (recommended) using
 * Application Default Credentials.
 */
$client->useApplicationDefaultCredentials();
$client->addScope(Google\Service\Plus::PLUS_ME);

// returns a Guzzle HTTP Client
$httpClient = $client->authorize();

// make an HTTP request
$response = $httpClient->get('https://www.googleapis.com/plus/v1/people/me');
```

### Caching ###

It is recommended to use another caching library to improve performance. This can be done by passing a [PSR-6](https://www.php-fig.org/psr/psr-6/) compatible library to the client:

```php
use League\Flysystem\Adapter\Local;
use League\Flysystem\Filesystem;
use Cache\Adapter\Filesystem\FilesystemCachePool;

$filesystemAdapter = new Local(__DIR__.'/');
$filesystem        = new Filesystem($filesystemAdapter);

$cache = new FilesystemCachePool($filesystem);
$client->setCache($cache);
```

In this example we use [PHP Cache](http://www.php-cache.com/). Add this to your project with composer:

```
composer require cache/filesystem-adapter
```

### Updating Tokens ###

When using [Refresh Tokens](https://developers.google.com/identity/protocols/OAuth2InstalledApp#offline) or [Service Account Credentials](https://developers.google.com/identity/protocols/OAuth2ServiceAccount#overview), it may be useful to perform some action when a new access token is granted. To do this, pass a callable to the `setTokenCallback` method on the client:

```php
$logger = new Monolog\Logger();
$tokenCallback = function ($cacheKey, $accessToken) use ($logger) {
  $logger->debug(sprintf('new access token received at cache key %s', $cacheKey));
};
$client->setTokenCallback($tokenCallback);
```

### Debugging Your HTTP Request using Charles ###

It is often very useful to debug your API calls by viewing the raw HTTP request. This library supports the use of [Charles Web Proxy](https://www.charlesproxy.com/documentation/getting-started/). Download and run Charles, and then capture all HTTP traffic through Charles with the following code:

Now all calls made by this library will appear in the Charles UI.

One additional step is required in Charles to view SSL requests. Go to **Charles > Proxy > SSL Proxying Settings** and add the domain you'd like captured. In the case of the Google APIs, this is usually `*.googleapis.com`.

### Controlling HTTP Client Configuration Directly

Google API Client uses [Guzzle](http://docs.guzzlephp.org/) as its default HTTP client. That means that you can control your HTTP requests in the same manner you would for any application using Guzzle.

Let's say, for instance, we wished to apply a referrer to each request.

Other Guzzle features such as [Handlers and Middleware](http://docs.guzzlephp.org/en/stable/handlers-and-middleware.html) offer even more control.

### Service Specific Examples ###

YouTube: https://github.com/youtube/api-samples/tree/master/php

