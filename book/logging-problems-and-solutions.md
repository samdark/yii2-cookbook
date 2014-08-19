Logging: problems and solutions
===============================

Logging in Yii is really flexible. Basics are easy but sometimes it takes time to configure
everything to get what you want. There are some ready to use solutions collected below.
Hopefully you'll find what you're looking for.

Write 404 to file and send the rest via email
---------------------------------------------

404 not found happens too often to email about it. Still, having 404s logged to a file could be
useful. Let's implement it.


```php
'components' => [
    'log' => [
        'targets' => [
            'file' => [
                'class' => 'yii\log\FileTarget',
                'categories' => ['yii\web\HttpException:404'],
                'levels' => ['error', 'warning'],
            ],
            'email' => [
                'class' => 'yii\log\EmailTarget',
                'except' => ['yii\web\HttpException:404'],
                'levels' => ['error', 'warning'],
                'message' => ['from' => 'robot@example.com', 'to' => 'admin@example.com'],
            ],
        ],
    ],
],
```

When there's unhandled exception in the application Yii logs it additionally to displaying it
to end user or showing customized error page. Exception message is what actually to be written and
the fully qualified exception class name is the category we can use to filter messages when
configuring targets. 404 can be triggered by throwing `yii\web\NotFoundHttpException` or automatically.
In both cases exception class is the same and is inherited from `\yii\web\HttpException` which is a bit
special in regards to logging. The speciality is the fact that HTTP status code prepended by `:` is
appended to the end of the log message category. In the above we're using `categories` to include
and `except` to exclude 404 log messages.

Immediate logging
-----------------

By default Yii accumulates logs till the script is finished or till the number of logs accumulated is
enough which is 1000 messages by default for both logger itself and log target. It could be that you
want to log messages immediately. For example, when running an import job and checking logs to see
the progress. In this case you need to change settings via application config file:

```php
'components' => [
    'log' => [
        'flushInterval' => 1, // <-- here
        'targets' => [
            'file' => [
                'class' => 'yii\log\FileTarget',
                'levels' => ['error', 'warning'],
                'exportInterval' => 1, // <-- and here
            ],
        ],
    ]
]
```
