Canonical URLs
==============

Because of many reasons the same or nearly the same page content often is accessible via multiple URLs. There are
valid cases for it such as viewing an article within a category and not so valid ones. For end user it doesn't really
matter much but still it could be a problem because of search engines because either you might get wrong URLs preferred
or, in the worst case, you might get penalized.

One way to solve it is to mark one of URLs as a primary or, as it called, canonical, one you may use `<link rel="canonical"` tag
in the page head.

> Note: In the above we assume that [pretty URLs are enabled](enable-pretty-urls.md).

Let's imagine we have two pages with similar or nearly similar content:

- `http://example.com/item1`
- `http://example.com/item2`

Our goal is to mark first one as canonical. Another one would be still accessible to end user. The process of adding
SEO meta-tags is descibed in "[adding SEO tags](adding-seo-tags.md)" recipe. Adding `<link rel="canonical"` is very
similar. In order to do it from controller action you may use the following code:

```php
\Yii::$app->view->registerLinkTag(['rel' => 'canonical', 'href' => Url::to(['item1'], true)]);
```

In order to achieve the same from inside the view do it as follows:

```php
$this->registerLinkTag(['rel' => 'canonical', 'href' => Url::to(['item1'], true)]);
```

> Note: It is necessary to use absolute paths instead of relative ones.

As an alternative to `Url::to()` you can use `Url::canonical()` such as

```php
$this->registerLinkTag(['rel' => 'canonical', 'href' => Url::canonical()]);
```

The line above could be added to layout. `Url::canonical()` generates the tag based on current controller route and
action parameters (the ones present in the method signature).

## See also

- [Google article about canonical URLs](https://support.google.com/webmasters/answer/139066?hl=en).
