Adding SEO tags
===============

Organic search is an excellent traffic source. In order to get it you have to make a lot of small steps to improve
your project.

One of such steps is to provide different meta tags for different pages. It will improve your site organic search
appearance and may result in better ranking.

Let's review how to add SEO-related metadata to your pages.

## Title

It is very simple to set title. Inside controller action:

```php
\Yii::$app->view->title = 'title set inside controller';
```

Inside a view:

```php
$this->title = 'Title from view';
```

> Note: Setting `$this->title` in layout will override value which is set for concrete view so don't do it.

It's a good idea to have default title so inside layout you can have something like the following:

```php
$this->title = $this->title ? $this->title : 'default title';
```
 
 
## Description and Keywords

There are no dedicated view parameters for `keywords` or  `description`. Since these are meta tags and you should
set them by `registerMetaTag()` method.


Inside controller action:

```php
\Yii::$app->view->registerMetaTag([
    'name' => 'description',
    'content' => 'Description set inside controller',
]);
\Yii::$app->view->registerMetaTag([
    'name' => 'keywords',
    'content' => 'Keywords set inside controller',
]);
```

Inside a view:

```php
$this->registerMetaTag([
    'name' => 'description',
    'content' => 'Description set inside view',
]);
$this->registerMetaTag([
    'name' => 'keywords',
    'content' => 'Keywords set inside view',
]);
```

All registered meta tags will be rendered inside layout in place of `$this->head()` call.

Note that when the same tag is registered twice it's rendered twice. For example, description meta tag that is registered
both in layout and a view is rendered twice. Usually it's not good for SEO. In order to avoid it you can specify key
as the second argument of `registerMetaTag`:

```php
$this->registerMetaTag([
    'name' => 'description',
    'content' => 'Description 1',
], 'description');

$this->registerMetaTag([
    'name' => 'description',
    'content' => 'Description 2',
], 'description');
```

In this case later second call will overwrite first call and description will be set to "Description 2".
