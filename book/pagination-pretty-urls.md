Pagination pretty URLs
=======================================

For example we can render our site content by GridView. If there are a lot of content rows we use pagination. 
And it is necessary to provide GET request for every pagination page. Thus search crawlers
can index our content. We also need our URLs to be pretty. Let's do it.

Initial state
---------
For example we have a page with such URL `http://example.com/schools/schoolTitle`.
Parameter `schoolTitle` is a `title` parameter for our request. For details see [this recipe](https://github.com/samdark/yii2-cookbook/blob/master/book/urls-variable-number-of-parameters.md).

In the application config file we have:
```php
$config = [
    // ...
    'components' => [
        // ...
        'urlManager' => [
            'showScriptName' => false,
            'enablePrettyUrl' => true,
            'rules' => [
              'schools/<title:\w+>' => 'site/schools',
            ],
        ],
        // ...
    ],
    // ...
```

We decided to add a GridView on the page `http://example.com/schools/schoolTitle`.

Pagination pretty URLs
---------
When we click on pagination link our URL is transformed to `http://example.com/schools/schoolTitle?page=2`.
We want our pagination link looks like `http://example.com/schools/schoolTitle/2`.

Let's add new urlManager rule **higher than** existed rule. Here it is:
```php
$config = [
        // ...
        'urlManager' => [
            // ...
            'rules' => [
              'schools/<title:\w+>/<page:\d+>' => 'site/schools', // new rule
              'schools/<title:\w+>' => 'site/schools',
            ],
        ],
        // ...
```
