WEB PAGE SEO TAGS
=======================================

Organic extradition is an excellent free traffic source. But in order to have such kind of traffic you have
to make a lot of steps to improve your web project.

One of such step is to provide different meta tags for different pages. It will improve
your  site organic extradition appearance.

Here is recipe about how to work with web page SEO tags.

Title
---------

It is very simple to set title. Of course you can also use PHP variables in order to compose dynamic page title.

Inside controller action:
```php
\Yii::$app->view->title = 'title set inside controller';
```

Inside view:
```php
$this->title = 'Title from view';
```


Notice, that setting `$this->title` in layout will override value which is set for concrete view.
 
 
Desctiption and Keywords
---------

There is no such view parameters as `keywords` or  `description`. There are meta tags and you should
set them by `registerMetaTag` method.


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

Inside view:

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

All registered meta tags will be rendered by default command inside layout:
```php
<?php $this->head() ?>
```

Notice, that if for ex. you register description meta tag both in layout and view you will have two meta keyword tags
on one page which is not good for SEO.


It is also good to have default SEO tags if your forget to set them on some pages. 
Inside layout you can have something like this:

```php
$this->title = $this->title ? $this->title : 'default title';
```