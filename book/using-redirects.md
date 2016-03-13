Using redirects
===============

## 301

Let's imagine we had a page `http://example.com/item2` but then permanently moved content to `http://example.com/item1`.
There is a good chance that some users (or search crawlers) have already saved `http://example.com/item2` via
bookmarks, database, web site article, etc. Because of that we can't just remove `http://webproject.ru/item2`.

In this case use 301 redirect.

```php
class MyController extends Controller
{
    public function beforeAction($action)
    {
        if (in_array($action->id, ['item2'])) {
            Yii::$app->response->redirect(Url::to(['item1']), 301);
            Yii::$app->end();
        }
        return parent::beforeAction($action);
    }
```

For further convenience you can determine an array. So if you need to redirect another URL then add new key=>value pair:

```php
class MyController extends Controller
{
    public function beforeAction($action)
    {
        $toRedir = [
            'item2' => 'item1',
            'item3' => 'item1',
        ];

        if (isset($toRedir[$action->id])) {
            Yii::$app->response->redirect(Url::to([$toRedir[$action->id]]), 301);
            Yii::$app->end();
        }
        return parent::beforeAction($action);
    }
```

## See also

- [Handling trailing slash in URLs](handling-trailing-slash-in-urls.md).