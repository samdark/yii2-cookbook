Cookies for subdomains
======================

Setting a cookie
----------------

This is how you usually set your cookies:

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

Cookies for subdomains
----------------------

`domain` parameter is empty by default, so cookies will only be accessible on the same domain from which they were set.
If you're planning to use subdomains (like one.example.com, two.example.com), you need to set `domain` explicitly:

```php
$cookie = new Cookie([
	'name' => 'cookie_monster',
	'value' => 'Me want cookie!',
	'expire' => time() + 86400 * 365,
	'domain' => '.example.com' // <<<=== HERE
]);
\Yii::$app->getResponse()->getCookies()->add($cookie);
```

Cross-subdomain auth and identity cookies
-----------------------------------------

The same is true for identity cookie. But this time you need to configure user component, setting `identityCookie` to desired cookie config.

Open you application config file and add `identityCookie` array to user component configuration:

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

