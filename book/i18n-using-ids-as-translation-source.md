Using IDs as translation source
================================

Default way of translating content in Yii is to use English as a source language. It is quite convenient but there's another convenient
way some developers prefer: using IDs such as `thank.you`.

## How to do it

Using keys is very easy. In order to do it, specify `sourceLanguage` as `key` in your message bundle config and
then set `forceTranslation` to `true`:

```php
'components' => [
    // ...
    'i18n' => [
        'translations' => [
            'app*' => [
                'class' => 'yii\i18n\PhpMessageSource',
                //'basePath' => '@app/messages',
                'sourceLanguage' => 'key', // <--- here
                'forceTranslation' => true, // <--- and here
                'fileMap' => [
                    'app' => 'app.php',
                    'app/error' => 'error.php',
                ],
            ],
        ],
    ],
],
```

Note that you have to provide translation for the application language (which is `en_US` by default) as well.

## How it works

Setting `sourceLanguage` to `key` makes sure no language will ever match so translation will always happen no matter what.
