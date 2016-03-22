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
                'logFile' => '@runtime/logs/404.log',
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

Write different logs to different files
-----------------

Usually a program has a lot of functions. Sometimes it is necessary to control these functions by logging. If everything is logged in one file this file becomes too big and too difficult to maintain. Good solution is to write different functions logs to different files.

For example you have two functions: catalog and basket. Let's write logs to catalog.log and basket.log respectively. In this case you need to establish categories for your log messages. Make a connection between them and log targets by changing application config file:

```php
'components' => [
    'log' => [
        'targets' => [
                [
                    'class' => 'yii\log\FileTarget',
                    'categories' => ['catalog'],
                    'logFile' => '@app/runtime/logs/catalog.log',
                ],
                [
                    'class' => 'yii\log\FileTarget',
                    'categories' => ['basket'],
                    'logFile' => '@app/runtime/logs/basket.log',
                ],
        ],
    ]
]
```

After this you are able to write logs to separate files adding category name to log function as second parameter. Examples: 

```php
\Yii::info('catalog info', 'catalog');
\Yii::error('basket error', 'basket');
\Yii::beginProfile('add to basket', 'basket');
\Yii::endProfile('add to basket', 'basket');
```

## Disable logging

### Problem
You want to receive email when new user signs up. But you don't want to trace yourself sign up tests.

### Solution

At first mark logging target inside `config.php` by key:
```php
'log' => [
    // ...
    'targets' => [
            // email is a key for our target
            'email' => [  
                'class' => 'yii\log\EmailTarget',
                'except' => ['yii\web\HttpException:404'],
                'levels' => ['info'],
                'message' => ['from' => 'robot@example.com', 'to' => 'admin@example.com'],
            ],
    // ...
```

Then, for example, inside `Controller` `beforeAction` you can create a condition:
```php
    public function beforeAction($action)
    {
        // '127.0.0.1' - replace by your IP address
        if (in_array(@$_SERVER['REMOTE_ADDR'], ['127.0.0.1'])) {
            Yii::$app->log->targets['email']->enabled = false; // Here we disable our log target
        }
        return parent::beforeAction($action);
    }
```