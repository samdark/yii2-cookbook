Reusing views via partials
=================

One of the main developing principle is DRY - don't repeat yourself. Sometimes duplications are appeared in views.
In order to fix duplications let's create reusable views.


Creating separate view
---------

There is a part of standard `views/site/index.php` code:

```php
<?php
/* @var $this yii\web\View */
$this->title = 'My Yii Application';
?>
<div class="site-index">
<div class="jumbotron">
    <h1>Congratulations!</h1>
    <p class="lead">You have successfully created your Yii-powered application.</p>
    <p><a class="btn btn-lg btn-success" href="http://www.yiiframework.com">Get started with Yii</a></p>
</div>
<div class="body-content">
//...
```

For example we want to show `<div class="jumbotron">` html block also inside `views/site/about.php` page.
Let's create separate view file `views/site/_jumbotron.php` and place inside it such code:

```html
<div class="jumbotron">
    <h1>Congratulations!</h1>
    <p class="lead">You have successfully created your Yii-powered application.</p>
    <p><a class="btn btn-lg btn-success" href="http://www.yiiframework.com">Get started with Yii</a></p>
</div>
```

Using separate view
---------
Regular `render()` method render both view file content and layout. Obviously for our situation layout is not
necessary. So let's use [renderPartial()](http://www.yiiframework.com/doc-2.0/yii-base-controller.html#renderPartial%28%29-detail) 
method, which render only view file content. 



Replace `<div class="jumbotron">` html block inside `views/site/index.php` by this code:

```php
<?php
/* @var $this yii\web\View */
$this->title = 'My Yii Application';
?>
<div class="site-index">
<?=$this->context->renderPartial('_jumbotron.php')?>; // this line replaces standard block
<div class="body-content">
//...
```

Please notice, that code `<?=$this->renderPartial('_jumbotron.php')?>;` will not work, because inside view variable `$this`
represents `View` class. And `renderPartial()` is a Controller method. So use `context` variable which represents
Controller of called `View`.


Let's add the same code line inside `views/site/about.php` (or inside another view):

```php
<?php
use yii\helpers\Html;
/* @var $this yii\web\View */
$this->title = 'About';
$this->params['breadcrumbs'][] = $this->title;
?>
<div class="site-about">
    <?=$this->context->renderPartial('_jumbotron.php')?>; // our line
```