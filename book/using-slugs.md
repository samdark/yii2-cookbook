Using slugs
===========

Even when pretty URLs are enabled, these often aren't looking too friendly:

```
http://example.com/post/42
```

Using Yii it doesn't take much time to make URLs look like the following:

```
http://example.com/post/hello-world
```

## Preparations

Set up database to use with Yii, create the following table:

```
post
====

id
title
content
```

Generate `Post` model and CRUD for it using Gii.

## How to do it

Add `slug` field to `post` table that holds our posts. Then add sluggable behavior to the model:

```php
<?php
use yii\behaviors\SluggableBehavior;

// ...

class Post extends ActiveRecord
{
    // ...

    public function behaviors()
    {
        return [
            [
                'class' => SluggableBehavior::className(),
                'attribute' => 'title',
            ],
        ];
    }
    
    // ...
}
```

Now when post is created `slug` in database will be automatically filled.

We need to adjust controller. Add the following method:

```php
protected function findModelBySlug($slug)
{
    if (($model = Post::findOne(['slug' => $slug])) !== null) {
        return $model;
    } else {
        throw new NotFoundHttpException();
    }
}
```

Now adjust `view` action:

```php
public function actionView($slug)
{
    return $this->render('view', [
        'model' => $this->findModelBySlug($slug),
    ]);
}
```

Now in order to create a link to the post you have to pass slug into it like the following:

```php
echo Url::to(['post/view', 'slug' => $post->slug]);
```

## Handling title changes

There are be multiple strategies to deal with situation when title is changed. One of these is to include ID in the title
and use it to find a post.

```
http://example.com/post/42/hello-world
```
