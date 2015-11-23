Handling trailing slash in URLs
===============================

By default Yii handles URLs without trailing slash and gives out 404 for URLs with it. It is a good idea to choose
either using or not using slash but handling both and doing 301 redirect from one variant to another.

For example,

```
/hello/world - 200
/hello/world/ - 301 redirect to /hello/world
```

## Redirecting to no-slash URLs

```php
'on beforeRequest' => function () {
    $pathInfo = Yii::$app->request->pathInfo;
    if (!empty($pathInfo) && substr($pathInfo, -1) === '/') {
        Yii::$app->response->redirect('/' . substr(rtrim($pathInfo), 0, -1), 301);
    }
},
```


## Redirecting to slash URLs


```php
'on beforeRequest' => function () {
    $pathInfo = Yii::$app->request->pathInfo;
    if (!empty($pathInfo) && substr($pathInfo, -1) !== '/') {
        Yii::$app->response->redirect('/' . rtrim($pathInfo) . '/', 301);
    }
},
```

TODO: handling it via webserver