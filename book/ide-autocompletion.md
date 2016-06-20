IDE autocompletion for custom components
========================================

Using IDE for development is quite typical nowadays because of the comfort it provides. It detects typos and errors, suggests code improvements and, of course, provides code autocomplete. For Yii 2.0 it works quite good out of the box but not in case of custom application components i.e. `Yii::$app->mycomponent->something`.

Using custom Yii class
----------------------

The best way to give IDE some hints is to use your own `Yii` file which isn't actually used when running code. This file could be
named `Yii.php` and the content could be the following:

```php
<?php
/**
 * Yii bootstrap file.
 * Used for enhanced IDE code autocompletion.
 */
class Yii extends \yii\BaseYii
{
    /**
     * @var BaseApplication|WebApplication|ConsoleApplication the application instance
     */
    public static $app;
}

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

In the above PHPDoc of `BaseApplication`, `WebApplication`, `ConsoleApplication` will be used by IDE to autocomplete your custom components described via `@property`.

> **Note**: To avoid "Multiple Implementations" PHPStorm warning and make autocomplete faster
> exclude or "Mark as Plain Text" `vendor/yiisoft/yii2/Yii.php` file.

That's it. Now `Yii::$app->user` will be our `\app\components\User` component instead of default one. The same applies for all other `@property`-declared components.

### Customizing User component

In order to get autocompletion for User's Identity i.e. `Yii::$app->user->identity`, `app\components\User` class should look like the following:

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
