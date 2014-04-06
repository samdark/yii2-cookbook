Configuring assets
==================

Yii is shipped with a lot of useful libraries, like jQuery and Twitter Bootstrap.
But sometimes we may want to replace them with their CDN version, or even use our local customized files. This comes in handy when we are using [bootstrap customizer](http://getbootstrap.com/customize/) for example.

This can be done quite easily by configuring asset manager component.

Replacing bundle files
----------------------

Go to your application config file and add this under 'components' section:

```php
$config = [
    // ...
    'components' => [
        // ...
        'assetManager' => [
            'bundles' => [
                'yii\web\JqueryAsset' => [
                    'sourcePath' => null,
                    'js' => ['//code.jquery.com/jquery-1.11.0.min.js'] // we'll take JQuery from CDN
                ],
                'yii\bootstrap\BootstrapAsset' => [
                    'sourcePath' => null,
                    'css' => [
                        'css/bootstrap.min.css',  // customized TWBS styles
                        'css/bootstrap-theme.css' // customized TWBS theme
                    ]
                ],
            ],
        ],
    ],
];
```

Disabling bundle files
----------------------

We can also prevent some asset files from being registered (for example, if we're planning to use our own css styles).
Just pass empty array as a value:

```php
$config = [
    // ...
    'components' => [
        // ...
        'assetManager' => [
            'bundles' => [
                'nodge\eauth\assets\WidgetAssetBundle' => [
                    'css' => [], // we remove EAuth asset css
                    // 'js' => [], JS files remain untouched
                ],
            ],
        ],
    ],
]
```

This way you can replace or disable assets for any bundle.
