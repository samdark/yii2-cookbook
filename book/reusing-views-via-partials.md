Reusing views via partials
==========================

One of the main developing principles is DRY - don't repeat yourself. Duplication happens everywhere during development
of the project including views. In order to fix it let's create reusable views.


## Creating partial view

Here's a part of a standard `views/site/index.php` code:

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

For example, we want to show `<div class="jumbotron">` HTML block both on the front page and inside `views/site/about.php`
view which is for *about* page. Let's create a separate view file `views/site/_jumbotron.php` and place the following
code inside:

```php
<div class="jumbotron">
    <h1>Congratulations!</h1>
    <p class="lead">You have successfully created your Yii-powered application.</p>
    <p><a class="btn btn-lg btn-success" href="http://www.yiiframework.com">Get started with Yii</a></p>
</div>
```

## Using partial view

Replace `<div class="jumbotron">` HTML block inside `views/site/index.php` with the following code:

```php
<?php
/* @var $this yii\web\View */
$this->title = 'My Yii Application';
?>
<div class="site-index">
<?=$this->render('_jumbotron.php')?>; // this line replaces standard block
<div class="body-content">
//...
```

Let's add the same code line inside `views/site/about.php` (or inside another view):

```php
<?php
use yii\helpers\Html;
/* @var $this yii\web\View */
$this->title = 'About';
$this->params['breadcrumbs'][] = $this->title;
?>
<div class="site-about">
    <?=$this->render('_jumbotron.php')?>; // our line
```

## Adding variables

Let's customize message displayed in jumbotron. By default it will be the same message but user should be able to pass
custom message via `message` parameter.

First of all, customize `views/site/_jumbotron.php`:

```php
<?php
$message = isset($message) ? $message : 'You have successfully created your Yii-powered application.';
?>
<div class="jumbotron">
    <h1>Congratulations!</h1>
    <p class="lead"><?= $message ?></p>
    <p><a class="btn btn-lg btn-success" href="http://www.yiiframework.com">Get started with Yii</a></p>
</div>
```

Now let's pass custom message for about page:


```php
<?php
use yii\helpers\Html;
/* @var $this yii\web\View */
$this->title = 'About';
$this->params['breadcrumbs'][] = $this->title;
?>
<div class="site-about">
    <?=$this->render('_jumbotron.php', [
        'message' => 'This is about page!',
    ])?>; // our line
```
