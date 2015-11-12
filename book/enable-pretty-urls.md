Enable pretty URLs
=======================================

Sometimes users want to share your site URLs on social networks. 
For example, by default your `about page` URL looks like `http://webproject.ru/index.php?r=site%2Fabout`.
Let's imagine this link on Facebook page. Do you want to click on it? Most of users don't know
what is `index.php` and what is `%2`. They will not trust this link, so will not click on it. Thus web site owner will lose
social network traffic.

Such URL is better: `http://webproject.ru/about`. Every user can understand that it is a clear way to go on
`about page`.

Let's enable pretty URL for our Yii project.


Apache Web server configuration
---------

Inside `.htaccess` file of your root web directory add following lines:

```
RewriteEngine on
# If a directory or a file exists, use it directly
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
# Otherwise forward it to index.php
RewriteRule . index.php
```


URL manager configuration
---------

Configure `urlManager component` inside your config file:
```php
    'components' => [
        // ...
        'urlManager' => [
            'class' => 'yii\web\UrlManager',
            // Disable index.php
            'showScriptName' => false,
            // Disable r= routes
            'enablePrettyUrl' => true,
            'rules' => [
                '<controller:\w+>/<id:\d+>' => '<controller>/view',
                '<controller:\w+>/<action:\w+>/<id:\d+>' => '<controller>/<action>',
                '<controller:\w+>/<action:\w+>' => '<controller>/<action>',
            ],
        ],
        // ...
    ],
```

Remove site parameter from URL
---------

After previous steps you will get such link `http://webproject.ru/site/about`. Parameter `site` tells nothing 
helpful to your users. So remove it by additional `urlManager` rule:
```php
    'rules' => [
        '<alias:\w+>' => 'site/<alias>',                    // new rule
        '<controller:\w+>/<id:\d+>' => '<controller>/view',
        '<controller:\w+>/<action:\w+>/<id:\d+>' => '<controller>/<action>',
        '<controller:\w+>/<action:\w+>' => '<controller>/<action>',
    ],
```

As result your URL will looks like `http://webproject.ru/about`.