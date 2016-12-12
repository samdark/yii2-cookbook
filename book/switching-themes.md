Switching themes dynamically
============================

View themes are useful for overriding extension views and making special view versions.
[Official Yii guide](http://www.yiiframework.com/doc-2.0/guide-output-theming.html) describes
static usage and configuration of views well so in this recipe we'll learn how to switch themes dynamically.

## Preparations

We'll start with the [basic project template](http://www.yiiframework.com/doc-2.0/guide-start-installation.html). Make
sure it is installed and works well.

## The goal

For simplicity, let's switch theme based on a GET parameter i.e. `themed=1`.
 
## How to do it

Theme could be switched at any moment before view template is rendered. For clarity sake let's do it right in
index action of `controllers/SiteController.php`:

```php
public function actionIndex()
{
    if (Yii::$app->request->get('themed')) {
        Yii::$app->getView()->theme = new Theme([
            'basePath' => '@app/themes/basic',
            'baseUrl' => '@web/themes/basic',
            'pathMap' => [
                '@app/views' => '@app/themes/basic',
            ],
        ]);
    }
    return $this->render('index');
}
```

If there's a `themed` GET parameter we're configuring current a theme which takes view templates
from `themes/basic` directory. Let's add customized template itself in `themes/basic/site/index.php`:


```php
Hello, I'm a custom theme!
```

That's it. Now try accessing homepage with and without `themed` GET parameter.
