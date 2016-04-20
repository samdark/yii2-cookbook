URLs with variable number of parameters
=======================================

There are many cases when you need to get variable number of parameters via URL.
For example one may want URLs such as `http://example.com/products/cars/sport` to lead to `ProductController::actionCategory`
where it's expected to get an array containing `cars` and `sport`.

Get Ready
---------

First of all, we need to enable pretty URLs. In the application config file add the following:

```php
$config = [
    // ...
    'components' => [
        // ...
        'urlManager' => [
            'showScriptName' => false,
            'enablePrettyUrl' => true,
            'rules' => require 'urls.php',
        ],
    ]
```

Note that we're including separate file instead of listing rules directly. It is helpful when application grows large.

Now in `config/urls.php` add the following content:

```php
<?php
return [
    [
        'pattern' => 'products/<categories:.*>',
        'route' => 'product/category',
        'encodeParams' => false,
    ],
];
```

Create `ProductController`:

```php
namespace app\controllers;

use yii\web\Controller;

class ProductController extends Controller
{
    public function actionCategory($categories)
    {
        $params = explode('/', $categories);
        print_r($params);
    }
}
```

That's it. Now you can try `http://example.com/products/cars/sport`. What you'll get is

```
Array ( [0] => cars [1] => sport)
```
