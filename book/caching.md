Caching
================

Yii supports different caching mechanisms. In this recipe we'll describe how to solve typical caching tasks. 

PHP variables caching using APC extension
----------------

Configure cache component in Yii application config file:
```php
'cache' => [
  'class' => 'yii\caching\ApcCache',
],
```

After that you can cache data by this way:

```php
$key = 'cacheKey'
$data = Yii::$app->cache->get($key);

if ($data === false) {
    // $data is not found in cache, calculate it from scratch

    // store $data in cache so that it can be retrieved next time
    $cache->set($key, $data);
}
```
Error `Call to undefined function yii\caching\apc_fetch()` means that you have problems with APC extension. Refer [PHP APC manual](http://php.net/manual/en/book.apc.php)
for the details.

If cache doesn't work (`$data is always false`) check `apc.shm_size` property in `php.ini`. Probably your data size is more than
allowed by this parameter.

HTTP caching for assets and other static resources
----------------

If expiration not specified for cacheable resources (`.js`, `.css`, .etc.) a speed of 
page loading process may be very slow.  Such tool as `PageSpeed Insights for Chrome` determines 
`expiration not specified` problem as **crucial** for yii web page performance. It advices you to 
`Leverage browser caching`. You can do it by adding only one row to your application
asset manager component:

```
return [
    // ...
    'components' => [
        'assetManager' => [
            'appendTimestamp' => true,
        ],
    ],
];
```