Handling incoming third party POST requests
===========================================

By default Yii uses CSRF protection that verifies that POST requests could be made only by the same application.
It enhances overall security significantly but there are cases when CSRF should be disabled i.e. when you expect
incoming POST requests from a third party service.

Additionally, if third party is posting via XMLHttpRequest (browser AJAX), we need to send additional headers to allow CORS ([cross-origin resource sharing](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)).

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

### Sending CORS headers

Yii has a [special Cors filter](http://www.yiiframework.com/doc-2.0/yii-filters-cors.html) that allows you sending headers
required to allow CORS.

To allow AJAX requests to the whole controller you can use it like that:

```php
class MyController extends Controller
{
    public function behaviors()
    {
        return [
            'corsFilter' => [
                'class' => \yii\filters\Cors::className(),
            ],
        ];
    }
```

In order to do it for a specific action, use the following:

```php
class MyController extends Controller
{
    public function behaviors()
    {
        return [
            'corsFilter' => [
                'class' => \yii\filters\Cors::className(),
                'cors' => [],
                'actions' => [
                    'incoming' => [
                        'Origin' => ['*'],
                        'Access-Control-Request-Method' => ['GET', 'POST', 'PUT', 'PATCH', 'DELETE', 'HEAD', 'OPTIONS'],
                        'Access-Control-Request-Headers' => ['*'],
                        'Access-Control-Allow-Credentials' => null,
                        'Access-Control-Max-Age' => 86400,
                        'Access-Control-Expose-Headers' => [],
                    ],
                ],
            ],
        ];
    }
```

