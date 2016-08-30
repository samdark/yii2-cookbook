Handling trailing slash in URLs
===============================

By default Yii handles URLs without trailing slash and gives out 404 for URLs with it. It is a good idea to choose
either using or not using slash but handling both and doing 301 redirect from one variant to another.

For example,

```
/hello/world - 200
/hello/world/ - 301 redirect to /hello/world
```

## Using UrlNormalizer

Since Yii 2.0.10 there's `UrlNormalizer` class you can use to deal with slash and no-slash URLs in a very convenient way.

See https://github.com/yiisoft/yii2/commit/6d15d4efe20d2fc2e219496976733944ae66ed4a#diff-e1294a469b930a9d8250fd039921d044R520

- TODO: give some usage examples
- TODO: deprecate "Yii prior to 2.0.10" section after 2.0.10 is released

## Yii prior to 2.0.10

Prior to 2.0.10 there was no built-in way to configure such redirections easily so it required extra effort.

### Redirecting to no-slash URLs

In order to prefer no-slash URLs add the following to `config/main.php` (or whatever your main application config file is):

```php
return [
    ...

    'on beforeRequest' => function () {
        $pathInfo = Yii::$app->request->pathInfo;
        $query = Yii::$app->request->queryString;
        if (!empty($pathInfo) && substr($pathInfo, -1) === '/') {
            $url = '/' . substr($pathInfo, 0, -1);
            if ($query) {
                $url .= '?' . $query;
            }
            Yii::$app->response->redirect($url, 301);
            Yii::$app->end();
        }
    },

    ...
];
```


### Redirecting to slash URLs

In order to prefer slash URLs add the following to `config/main.php` (or whatever your main application config file is):

```php
return [
    ...
    
    'on beforeRequest' => function () {
        $pathInfo = Yii::$app->request->pathInfo;
        $query = Yii::$app->request->queryString;
        if (!empty($pathInfo) && substr($pathInfo, -1) !== '/') {
            $url = '/' . $pathInfo . '/';
            if ($query) {
                $url .= '?' . $query;
            }
            Yii::$app->response->redirect($url, 301);
            Yii::$app->end();
        }
    },
    
    ...
];
```

## Redirecting via web server config

Besides PHP, there's a way to achieve redirection using web server.

## Redirecting via nginx

Redirecting to no-slash URLs.

```
location / {
    rewrite ^(.*)/$ $1 permanent;
    try_files $uri $uri/ /index.php?$args;
}
```

Redirecting to slash URLs.

```
location / {
    rewrite ^(.*[^/])$ $1/ permanent;
    try_files $uri $uri/ /index.php?$args;
}
```

## Redirecting via Apache

Redirecting to no-slash URLs.

```
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)/$ /$1 [L,R=301]
```

Redirecting to slash URLs.

```
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^(.*[^/])$ /$1/ [L,R=301]
```
