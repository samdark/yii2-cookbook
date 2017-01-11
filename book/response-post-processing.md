Post-processing response
========================

Sometimes there's a need to post-process repsonse which is to be sent to browser. A good example is short tags like
the ones in Wordpress engine. You use it like the following:

```php
This is [username]. We have [visitor_count] visitors on website.
```

And both are automatically replaced by corresponding content.

## How to do it

Yii is very flexible so it's easy to achieve:

```php
Yii::$app->getResponse()->on(Response::EVENT_AFTER_PREPARE, function($event) {
    /** @var User $user */
    $user = Yii::$app->getUser()->getIdentity();
    $replacements = [
        '[username]' => $user->username,
        '[visitor_count]' => 42,
    ];

    $event->sender->content = str_replace(array_keys($replacements), array_values($replacements), $event->sender->content);
});
```

In the code above we're using `Response::EVENT_AFTER_PREPARE` which is triggered right before sending content to a browser.
In the callback `$event->sender` is our response object which keeps data to be sent in `content` property. So we are
finding and replacing short tags there.
