Working with different response types
=====================================

Response formats
----------------

As you probably know, in Yii2 you need to `return()` the result from your action, instead of echoing it directly:

```php
// returning HTML result
return $this->render('index', [
    'items' => $items,
]);
```

Good thing about it is now you can return different types of data from your action, namely:

- an array
- an object implementing `Arrayable` interface
- a string
- an object implementing `__toString()` method.

Just don't forget to tell Yii what format do you want as result, by setting `\Yii::$app->response->format` before `return()`. For example:
```php
\Yii::$app->response->format = \yii\web\Response::FORMAT_JSON;
```

Valid formats are:

- FORMAT_RAW
- FORMAT_HTML
- FORMAT_JSON
- FORMAT_JSONP
- FORMAT_XML

Default is `FORMAT_HTML`.


JSON response
-------------

Let's return an array:

```php
public function actionIndex()
{
    \Yii::$app->response->format = \yii\web\Response::FORMAT_JSON;
    $items = ['some', 'array', 'of', 'data' => ['associative', 'array']];
    return $items;
}
```

And - voila! - we have JSON response right out of the box:

**Result:**
```
{
    "0": "some",
    "1": "array",
    "2": "of",
    "data": ["associative", "array"]
}
```

(Notice that you'll get an exception if response format is not set).

As I've said before, we can return objects too.

```php
public function actionView($id)
{
    \Yii::$app->response->format = \yii\web\Response::FORMAT_JSON;
    $user = \app\models\User::find($id);
    return $user;
}
```

Now $user is an object of `ActiveRecord` class that implements `Arrayable` interface, so it can be easily converted to JSON.

**Result:**
```
{
    "id": 1,
    "name": "John Doe",
    "email": "john@example.com"
}
```

We can even return an array of objects:

```php
public function actionIndex()
{
    \Yii::$app->response->format = \yii\web\Response::FORMAT_JSON;
    $users = \app\models\User::find()->all();
    return $users;
}
```

Now $users is an array of ActiveRecord objects, but under the hood Yii uses `\yii\helpers\Json::encode()` to traverse and convert the returned data.

**Result:**
```
[
    {
        "id": 1,
        "name": "John Doe",
        "email": "john@example.com"
    },
    {
        "id": 2,
        "name": "Jane Foo",
        "email": "jane@example.com"
    },
    ...
]
```

XML response
------------

Just change response format to `FORMAT_XML` and that't it. Now you have XML:
```php
public function actionIndex()
{
    \Yii::$app->response->format = \yii\web\Response::FORMAT_XML;
    $items = ['some', 'array', 'of', 'data' => ['associative', 'array']];
    return $items;
}
```

**Result:**
```xml
<response>
    <item>some</item>
    <item>array</item>
    <item>of</item>
    <data>
        <item>associative</item>
        <item>array</item>
    </data>
</response>
```

And yes, we can convert objects and array of objects the same way as we did before.
```php
public function actionIndex()
{
    \Yii::$app->response->format = \yii\web\Response::FORMAT_XML;
    $users = \app\models\User::find()->all();
    return $items;
}
```
**Result:**
```xml
<response>
    <User>
        <id>1</id>
        <name>John Doe</name>
        <email>john@example.com</email>
    </User>
    <User>
        <id>2</id>
        <name>Jane Foo</name>
        <email>jane@example.com</email>
    </User>
</response>
```

Custom response
---------------

TBD
