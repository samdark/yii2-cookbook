XSS
================

Cross-site scripting (XSS) is a typical web application type of vulnerability. It allows attacker to inject client-side
script into your site web pages. For example, your site has guest book. Some user can add this text as a comment:

```javascript
<script>
    alert('hello from hacker')
<script>
```
If you allow your web site to publish a comment 'as is' then every user who visits your guest book will see 
'Hello from hacker' alert box.

Here is a very simple example.

Let's add to controller:

```php
public function actionIndex()
{
    $post = '<script>alert("client script injection example")</script>';
    return $this->render('index', [
    'post'=>$post,
    ]);
}
```
Then let's add to index.php view:

```php
echo $post;
```

If you go to your site main page you will see 'client script injection example' alert. Thus there is a possibility
to inject script into your application. For example php session cookie can be stolen this way.

How to prevent XSS?

PHP built-in function
----------------

You can use `strip_tags` function:
```php
public function actionIndex()
{
    $post = '<script>alert("client script injection example")</script>';
    
    $post = strip_tags($post); // here we do it 
    return $this->render('index', [
    'post'=>$post,
    ]);
}
```

So instead of alert you will see `alert("client script injection example")` line as a comment message.

But you do not want to show/save in DB such `comments`. How to check is a comment an XSS or not?

HtmlPurifier
----------------
Let's use YII2 helper class [HtmlPurifier](http://www.yiiframework.com/doc-2.0/yii-helpers-basehtmlpurifier.html).

Modify your sample action code:
```php
public function actionIndex()
{
    $post = '<script>alert("client script injection example")</script>';
    $post = HtmlPurifier::process($post); // here we do it. Now $post contains empty string.
    return $this->render('index', [
    'post'=>$post,
    ]);
}
```

Inside view:
```php
if ($post) echo $post;
```

As the result you will see nothing related to script. 
Code above is just an example how to HtmlPurifier works, not a coding best practice. 
You have to organize if-statements in a proper for you web application way.

Note: 

Maybe you have an idea to use HtmlPurifier to clean csv data input contains a lot of rows. Be ready for performance problems.
Here is an example:  

```php
$a = [];
for ($i = 0; $i < 1000; $i++) {
    $a[] = $i;
}
$time = microtime(1);
foreach($a as &$item) {
    $item = HtmlPurifier::process($item);
}
echo microtime(1) - $time;
```

HtmlPurifier is using a lot of regular expressions to clean the request. In example above script execution time is around 4 seconds.
If array is bigger then such cleaning can be a main bottleneck for your performance.

See also
--------

- [OWASP article about XSS](https://www.owasp.org/index.php/Cross-site_Scripting_%28XSS%29)
- [Yii2 helper class HtmlPurifier](http://www.yiiframework.com/doc-2.0/yii-helpers-basehtmlpurifier.html).
- [HtmlPurifier web site](http://htmlpurifier.org)
