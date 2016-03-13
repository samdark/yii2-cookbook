XSS
===

Cross-site scripting (XSS) is a web application vulnerability caused by insufficient output escaping. It allows attacker
to inject JavaScript code into your site pages. For example, if your website has comments, an attacker may add the
following text as a comment:

```javascript
<script>alert('Hello from hacker ;)');</script>
```

If there's no filtering and comment is published as is, every user who visits the page will get
"Hello from hacker" alert box which means JavaScript is executed. And with JavaScript attacker can do virtually anything
valid user can.

That's how it typically looks in Yii. In controller we're getting data and passing it to view:

```php
public function actionIndex()
{
    $data = '<script>alert("injection example")</script>';
        return $this->render('index', [
        'data' => $data,
    ]);
}
```

And in `index.php` view we output data without any escaping:

```php
echo $post;
```

That's it. We're vulnerable. Visit your website main page you will see "injection example" alert.

Next you'll learn how to prevent it.

## Basic output escaping

If you're sure you'll have just text in your data, you can escape it in the view with `Html::encode()` while outputting it:

 ```php
 echo Html::encode($post);
 ```

## Dealing with HTML output

In case you need to output HTML entered by user it's getting a bit more complicated. Yii has a built in
[HtmlPurifier helper](http://www.yiiframework.com/doc-2.0/yii-helpers-basehtmlpurifier.html) which cleans up everything
dangerous from HTML. In a view you may use it as the following:

```php
echo HtmlPurifier::process($post);
```

> Note: HtmlPurifier isn't fast so consider caching what's produced by `HtmlPurifier` not to call it too often.

See also
--------

- [OWASP article about XSS](https://www.owasp.org/index.php/Cross-site_Scripting_%28XSS%29).
- [HtmlPurifier helper class](http://www.yiiframework.com/doc-2.0/yii-helpers-basehtmlpurifier.html).
- [HtmlPurifier website](http://htmlpurifier.org).
