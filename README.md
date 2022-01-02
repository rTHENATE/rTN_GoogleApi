# API

## Updating Tokens ###

When using [Refresh Tokens](https://developers.google.com/identity/protocols/OAuth2InstalledApp#offline) or [Service Account Credentials](https://developers.google.com/identity/protocols/OAuth2ServiceAccount#overview), it may be useful to perform some action when a new access token is granted. To do this, pass a callable to the `setTokenCallback` method on the client:

### Service Specific Examples ###

YouTube: https://github.com/youtube/api-samples/tree/master/php

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

## Installation ##

You can use **Composer** or simply **Download the Release**


### Download the Release

If you prefer not to use composer, you can download the package in its entirety. The [Releases](https://github.com/googleapis/google-api-php-client/releases) page lists all stable versions. Download any file
with the name `google-api-php-client-[RELEASE_NAME].zip` for a package including this library and its dependencies.

Uncompress the zip file you download, and include the autoloader in your project:
