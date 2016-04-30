Managing cookies
================

Managing HTTP cookies isn't that hard using plain PHP but Yii makes it a bit more convenient. In this recipe we'll describe how to perform typical cookie actions.

Setting a cookie
----------------

To set a cookie i.e. to create it and schedule for sending to the browser you need to create new `\yii\web\Cookie` class instance and add it to response cookies collection:

```php
$cookie = new Cookie([
    'name' => 'cookie_monster',
    'value' => 'Me want cookie!',
    'expire' => time() + 86400 * 365,
]);
\Yii::$app->getResponse()->getCookies()->add($cookie);
```

In the above we're passing parameters to cookie class constructor. These basically the same as used with native PHP [setcookie](http://php.net/manual/en/function.setcookie.php) function:

- `name` - name of the cookie.
- `value` - value of the cookie. Make sure it's a string. Browsers typically aren't happy about binary data in cookies.
- `domain` - domain you're setting the cookie for.
- `expire` - unix timestamp indicating time when the cookie should be automatically deleted.
- `path` - the path on the server in which the cookie will be available on.
- `secure` - if `true`, cookie will be set only if HTTPS is used.
- `httpOnly` - if `true`, cookie will not be available via JavaScript.

Reading a cookie
----------------

In order to read a cookie use the following code:

```php
$value = \Yii::$app->getRequest()->getCookies()->getValue('my_cookie');
```

Where to get and set cookies?
-----------------------------

Cookies are part of HTTP request so it's a good idea to do both in controller which responsibility is exactly dealing with request and response.

Cookies for subdomains
----------------------

Because of security reasons, by default cookies are accessible only on the same domain from which they were set.
For example, if you have set a cookie on domain `example.com`, you cannot get it on domain `www.example.com`.
So if you're planning to use subdomains (i.e. admin.example.com, profile.example.com), you need to set `domain`
explicitly:

```php
$cookie = new Cookie([
	'name' => 'cookie_monster',
	'value' => 'Me want cookie everywhere!',
	'expire' => time() + 86400 * 365,
	'domain' => '.example.com' // <<<=== HERE
]);
\Yii::$app->getResponse()->getCookies()->add($cookie);
```

Now cookie can be read from all subdomains of `example.com`.

Cross-subdomain authentication and identity cookies
---------------------------------------------------

In case of autologin or "remember me" cookie, the same quirks as in case of subdomain cookies are applying.
But this time you need to configure user component, setting `identityCookie` array to desired cookie config.

Open you application config file and add `identityCookie` parameters to user component configuration:

```php
$config = [
    // ...
    'components' => [
        // ...
        'user' => [
            'class' => 'yii\web\User',
            'identityClass' => 'app\models\User',
            'enableAutoLogin' => true,
            'loginUrl' => '/user/login',
            'identityCookie' => [ // <---- here!
                'name' => '_identity',
                'httpOnly' => true,
                'domain' => '.example.com',
            ],
        ],
        'request' => [
            'cookieValidationKey' => 'your_validation_key'
        ],
        'session' => [
            'cookieParams' => [
                'domain' => '.example.com',
                'httpOnly' => true,
            ],
        ],

    ],
];
```

Note that `cookieValidationKey` should be the same for all sub-domains.

Note that you have to configure the `session::cookieParams` property to have the samedomain as your `user::identityCookie` to ensure the `login` and `logout` work for all subdomains. This behavior is better explained on the next section.

Session cookie parameters
-------------------------

Session cookies parameters are important both if you have a need to maintain session while getting from one
subdomain to another or when, in contrary, you host backend app under `/admin` URL and want handle session
separately.

```php
$config = [
    // ...
    'components' => [
        // ...
        'session' => [
            'name' => 'admin_session',
            'cookieParams' => [
                'httpOnly' => true,
                'path' => '/admin',
            ],
        ],
    ],
];
```


See also
--------

- [API reference](http://stuff.cebe.cc/yii2docs/yii-web-cookie.html)
- [PHP documentation](http://php.net/manual/en/function.setcookie.php)
- [RFC 6265](http://www.faqs.org/rfcs/rfc6265.html)
