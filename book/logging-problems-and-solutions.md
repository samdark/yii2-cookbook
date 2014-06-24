Logging: problems and solutions
===============================

Log everything except 404
-------------------------

https://github.com/yiisoft/yii2/issues/1461

```php
'components.log' => [
    'traceLevel' => 0,
    'targets' => [
        'file' => [
        'class' => 'yii\log\FileTarget',
        'levels' => ['error', 'warning'],
    ],
    'email' => [
        'class' => 'yii\log\EmailTarget',
        'except' => ['yii\web\HttpException:404'],
        'levels' => ['error', 'warning'],
        'message' => ['from' => 'webmaster@mydomain.com', 'to' => 'webmaster@mydomain.com'],
    ],
],
```
