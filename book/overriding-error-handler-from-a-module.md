# Overriding error handler from a module

In some cases you want a custom error handler to be used for particular module.

Yii doesn't have support for module-based error handling. The error handler is
global i.e. there's only one for the whole application that is already registered
by the time a module is executed. Therefore we need to both overwrite `errorHandler`
component, set it for a current application and register it as a PHP error handler.
Here's how to do it:

```php
class Module extends \yii\base\Module
{
    public function init()
    {
        parent::init();
        \Yii::configure($this, [
            'components' => [
                'errorHandler' => [
                    'class' => ErrorHandler::className(),
                ]
            ],
        ]);

        /** @var ErrorHandler $handler */
        $handler = $this->get('errorHandler');
        \Yii::$app->set('errorHandler', $handler);
        $handler->register();
    }
}
```

