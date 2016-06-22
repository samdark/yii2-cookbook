Using Yii in third party apps
=============================

Legacy code. It happened to all of us. It's hard but we still need to deal with it.
Would it be cool to gradually move from legacy towards Yii? Absolutely.

In this recipe you'll learn how to use Yii features in an existing PHP application.

How to do it
------------

First of all, we need Yii itself. Since existing legacy application already takes care about routing,
we don't need any application template. Let's start with `composer.json`. Either use existing one or create it:

```javascript
{
    "name": "mysoft/mylegacyapp",
    "description": "Legacy app",
    "keywords": ["yii2", "legacy"],
    "homepage": "http://www.example.com/",
    "type": "project",
    "license": "Copyrighted",
    "minimum-stability": "dev",
    "require": {
        "php": ">=5.4.0",
        "yiisoft/yii2": "*"
    },
    "config": {
        "process-timeout": 1800
    },
    "extra": {
        "asset-installer-paths": {
            "npm-asset-library": "vendor/npm",
            "bower-asset-library": "vendor/bower"
        }
    }
}
```

Now run `composer install` and you should get Yii in `vendor` directory.

> Note: The dir should not be accessible from the web. Either keep it out of webroot
or deny directory access.

Now create a file that will initialize Yii. Let's call it `yii_init.php`:

```php
<?php
// set it to false when in production
defined('YII_DEBUG') or define('YII_DEBUG', true);

require(__DIR__ . '/vendor/autoload.php');
require(__DIR__ . '/vendor/yiisoft/yii2/Yii.php');

$config = require(__DIR__ . '/config/yii.php');

new yii\web\Application($config);
```

Now create a config `config/yii.php`:

```php
<?php

return [
    'id' => 'myapp',
    'basePath' => dirname(__DIR__),
    'bootstrap' => ['log'],
    'components' => [
        'request' => [
            // !!! insert a secret key in the following (if it is empty) - this is required by cookie validation
            'cookieValidationKey' => '',
        ],
        'cache' => [
            'class' => 'yii\caching\FileCache',
        ],
        'user' => [
            'identityClass' => 'app\models\User',
            'enableAutoLogin' => true,
        ],
        'log' => [
            'traceLevel' => YII_DEBUG ? 3 : 0,
            'targets' => [
                [
                    'class' => 'yii\log\FileTarget',
                    'levels' => ['error', 'warning'],
                ],
            ],
        ],
    ],
    'params' => [],
];
```

That's it. You can require the file and mix Yii code into your legacy app:

```php
// legacy code

$id = (int)$_GET['id'];


// new code
require 'path/to/yii_init.php';

$post = \app\models\Post::find()->where['id' => $id];

echo Html::encode($post->title);
```

How it works
------------

In the `yii_init.php` we are including framework classes and composer autoloading. Important part there is that `->run()`
method isn't called like it's done in normal Yii application. That means that we're skipping routing, running controller
etc. Legacy app already doing it.
