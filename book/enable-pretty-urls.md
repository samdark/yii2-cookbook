Enable pretty URLs
==================

Sometimes users want to share your site URLs via social networks. For example, by default your *about page* URL looks
like `http://webproject.ru/index.php?r=site%2Fabout`. Let's imagine this link on Facebook page. Do you want to click
on it? Most of users have no idea what is `index.php` and what is `%2`. They trust such link less, so will click less
on it. Thus web site owner would lose traffic.

URLs such as the following is better: `http://webproject.ru/about`. Every user can understand that it is a clear way
to get to *about page*.

Let's enable pretty URLs for our Yii project.


## Apache Web server configuration

If you're using Apache you need an extra step. Inside your `.htaccess` file in your webroot directory or inside location
section of your main Apache config add the following lines:

```
RewriteEngine on
# If a directory or a file exists, use it directly
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
# Otherwise forward it to index.php
RewriteRule . index.php
```


## URL manager configuration

Configure `urlManager` component in your Yii config file:

```php
'components' => [
    // ...
    'urlManager' => [
        'class' => 'yii\web\UrlManager',
        // Hide index.php
        'showScriptName' => false,
        // Use pretty URLs
        'enablePrettyUrl' => true,
        'rules' => [
        ],
    ],
    // ...
],
```

### Remove site parameter from URL

After previous steps you will get `http://webproject.ru/site/about` link. `site` parameter tells nothing helpful to
your users. So remove it by additional `urlManager` rule:

```php
    'rules' => [
        '<alias:\w+>' => 'site/<alias>',
    ],
```

As a result your URL will looks like `http://webproject.ru/about`.
