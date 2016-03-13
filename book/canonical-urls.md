Canonical URLs
===============

Sometimes it is convenient or necessary for the same content to be accessed through
multiple URLS. For example:
If we have only php_and_mysql books then page content will be the same for such URLs:
- `http://webproject.ru/books?category=php`
- `http://webproject.ru/books?category=mysql`

It is ok for you shop but not good for the search engines. But crawler time to index your site is always limited.
So crawler will waste its indexing time on useless for your search appearance pages.

There are different ways to solve this problem.

For all examples pretty URLs are enabled. Please refer to [Enable pretty URLs](enable-pretty-urls.md)

## Set preferred URL with rel="canonical" link element

Mark preferred (most valuable for SEO) URL with this tag and help search crawler to distribute limited indexing time
on other pages.

Let's imagine we have two pages with mostly similar content:
`http://webproject.ru/item1`
`http://webproject.ru/item2`

Our goal is make first link canonical but remain second link available for user.

[Adding SEO tags](adding-seo-tags.md) recipe describes how add seo meta tags. Adding rel="canonical" tag is very similar.

Inside controller action:
```php
\Yii::$app->view->registerLinkTag(['rel' => 'canonical', 'href' => Url::to(['item1'], true)]);
```

Or inside a view:
```php
$this->registerLinkTag(['rel' => 'canonical', 'href' => Url::to(['item1'], true)]);
```

> Note: It is necessary to use absolute paths (search engine rules). Do not use relative paths.

## Use 301 redirect to avoid duplications

Similar case is explained in recipe of [Handling trailing slash in URLs](handling-trailing-slash-in-urls.md)

Let's imagine at first we have a page `http://webproject.ru/item2`.

But then we permanently move a content to `http://webproject.ru/item1`.

There is a risk that some users (or search crawlers) have already saved `http://webproject.ru/item2` somewhere 
(bookmarks, search engine base, web site article, etc.)

So we can't just disable the link `http://webproject.ru/item2`.

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

For the further convenience you can determine an array. So if you need to redirect another URL then add new key=>value pair:
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

- [Google article about canonical URLs](https://support.google.com/webmasters/answer/139066?hl=en)
- [Handling trailing slash in URLs](handling-trailing-slash-in-urls.md)