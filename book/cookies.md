Managing cookies
================

Setting a cookie
----------------

To set a cookie, create new `\yii\web\Cookie` instance and add it to response cookies collection:

```php
$cookie = new Cookie([
	'name' => 'cookie_monster',
	'value' => 'Me want cookie!',
	'expire' => time() + 86400 * 365
]);
\Yii::$app->getResponse()->getCookies()->add($cookie);
```

Valid cookie config parameters are:

- name
- value
- domain
- expire
- path
- secure
- httpOnly

See [API reference](http://stuff.cebe.cc/yii2docs/yii-web-cookie.html) for detailed explanation of these parameters.


Reading a cookie
----------------

...

```php
$value = \Yii::$app->getRequest()->getCookies()->getValue('my_cookie');
```

Cookies for subdomains
----------------------

Because of security reasons, cookies are accessible by default only on the same domain from which they were set.
For example, if you have set a cookie on domain `example.com`, you cannot get it on domain `www.example.com`.
So if you're planning to use subdomains (like admin.example.com, profile.example.com), you need to set `domain` explicitly:

```php
$cookie = new Cookie([
	'name' => 'cookie_monster',
	'value' => 'Me want cookie everywhere!',
	'expire' => time() + 86400 * 365,
	'domain' => '.example.com' // <<<=== HERE
]);
\Yii::$app->getResponse()->getCookies()->add($cookie);
```

Now cookie can be read from all subdomains.

Cross-subdomain auth and identity cookies
-----------------------------------------

The same is true for identity cookie, that is used for autologin.
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
		    // HERE:
		    'identityCookie' => [
		    	'name' => '_identity',
		    	'httpOnly' => true,
		    	'domain' => '.example.com'
		    ]
		],
	],
];
```

Now identity cookie is accessible on all subdomains.

Session cookie params
---------------------

???
TBD

See also
--------

[API reference](http://stuff.cebe.cc/yii2docs/yii-web-cookie.html)
[TBD: Yii documentation](https://github.com/yiisoft/yii2/blob/master/docs/guide/security.md#securing-cookies)
[PHP documentation](http://php.net/manual/en/function.setcookie.php)
[RFC 6265](http://www.faqs.org/rfcs/rfc6265.html)

