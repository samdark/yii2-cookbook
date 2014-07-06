IDE autocompletion for custom components
===============================

This recipe describes how to get IDE autocompletion for `Yii::$app->user->identity` and custom components.

-------------------------

In most of cases during developing application based on Yii, there is need to extend some framework Core components.
It becomes annoying when there is no hints for `Yii::$app->user->identity`, like methods and properies we set in our User's model.

Fortunately, Yii2 designed in way allowing to add IDE support for these custom extended classes.
There is file `./vendor/yiisoft/yii2/Yii.php` which is included to start the application.

To have IDE support create copy of that file and include it in your application's index.php instead of core `Yii.php` file.

new file: `Yii.php`:
```php
<?php
/**
 * Yii bootstrap file.
 * Used for IDE code autocompletion for our extended from Yii core components and classes.
 *
 * This file is copy and does the same as './vendor/yiisoft/yii2/Yii.php'.
 * Note! To avoid 'Multiple Implementations' PHPStorm waring and make autocomplete faster
 *  - exclude or 'Mark as Plain Text' file './vendor/yiisoft/yii2/Yii.php'.
 *
 *
 * Yii is a helper class serving common framework functionalities.
 *
 * It extends from [[\yii\BaseYii]] which provides the actual implementation.
 * By writing your own Yii class, you can customize some functionalities of [[\yii\BaseYii]].
 *
 * @author Qiang Xue <qiang.xue@gmail.com>
 * @since 2.0
 */
class Yii extends \yii\BaseYii
{
    /**
     * @var BaseApplication|WebApplication|ConsoleApplication the application instance
     */
    public static $app;
}

spl_autoload_register(['Yii', 'autoload'], true, true);
Yii::$classMap = include(__DIR__ . '/vendor/yiisoft/yii2/classes.php');
Yii::$container = new yii\di\Container;

/**
 * Class BaseApplication
 * Used for properties that are identical for both WebApplication and ConsoleApplication
 *
 * @property \app\components\RbacManager $authManager The auth manager for this application. Null is returned if auth manager is not configured. This property is read-only. Extended component.
 * @property \app\components\Mailer $mailer The mailer component. This property is read-only. Extended component.
 */
abstract class BaseApplication extends yii\base\Application
{
}

/**
 * Class WebApplication
 * Include only Web application related components here
 *
 * @property \app\components\User $user The user component. This property is read-only. Extended component.
 * @property \app\components\MyResponse $response The response component. This property is read-only. Extended component.
 * @property \app\components\ErrorHandler $errorHandler The error handler application component. This property is read-only. Extended component.
 */
class WebApplication extends yii\web\Application
{
}

/**
 * Class ConsoleApplication
 * Include only Console application related components here
 *
 * @property \app\components\ConsoleUser $user The user component. This property is read-only. Extended component.
 */
class ConsoleApplication extends yii\console\Application
{
}
```

Now `Yii::$app->user` component is extended. To get autocompletion for User's Identity `Yii::$app->user->identity`, `app\components\User` class should look this way:
```php
<?php

namespace app\components;

use Yii;

/**
 * @inheritdoc
 *
 * @property \app\models\User|\yii\web\IdentityInterface|null $identity The identity object associated with the currently logged-in user. null is returned if the user is not logged in (not authenticated).
 */
class User extends \yii\web\User
{
}
```

As a result, Yii config file may look this way:
```php
return [
    ...
    'components' => [
        /**
         * User
         */
        'user' => [
            'class' => 'app\components\User',
            'identityClass' => 'app\models\User',
        ],
        /**
         * Custom Component
         */
        'response' => [
            'class' => 'app\components\MyResponse',
        ],
    ],
];
```
