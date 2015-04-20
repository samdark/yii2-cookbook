Handling incoming third party POST requests
===========================================

By default Yii uses CSRF protection that verifies that POST requests could be made only by the same application.
It enhances overall security significantly but there are cases when CSRF should be disabled i.e. when you expect
incoming POST requests from a third party serice.

How to do it
------------

First of all, never disable CSRF protection altogether. If you need it to be disabled, do it for specific controller or even
controller action.

### Disabling CSRF for a specific controller

Disabling protection for specific controller is easy:

```php
class MyController extends Controller
{
    public $enableCsrfValidation = false;
```

We've added a public property `enableCsrfValidation` and set it to false.

### Disabling CSRF for specific controller action

In case of disabling only a single action it's a bit more code:

```php
class MyController extends Controller
{
    public function beforeAction($action)
    {
        if (in_array($action->id, ['incoming'])) {
            $this->enableCsrfValidation = false;
        }
        return parent::beforeAction($action);
    }
```

We've implemented `beforeAction` controller method. It is invoked right before an action is executed so we're
checking if executed action id matches id of the action we want to disable CSRF protection for and, if it's true,
disabling it. Note that it's important to call parent method and call it last.
