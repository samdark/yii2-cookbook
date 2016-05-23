Using machine translation (such as google or yandex) as translation source.
================================

Sometimes we need to translate the site into other languages.
I do not recommend to use machine translation as production ready solution, but it can help to prepare the site to the translation by professional translators.


## How to do it

First of all we need catch MissingTranslationEvent. It is more convenient to wrap catch method in component.
We use DbMessageSource to store translated messages ([the appropriate database migration is needed](http://www.yiiframework.com/doc-2.0/yii-i18n-dbmessagesource.html)).

```php
namespace common\components;

use yii\db\Query;
use yii\helpers\ArrayHelper;
use yii\i18n\MissingTranslationEvent;

class YandexTranslation extends \yii\base\Component
{
        /**
         * Provider api key. Get it for free at
         * @link https://tech.yandex.com/keys/get/?service=trnsl
         */
        public $apiKey;

        /**
         * @param MissingTranslationEvent $event
         */
        public function handleMissingTranslation(MissingTranslationEvent $event)
        {
                /* @var $messageSource \yii\i18n\DbMessageSource */
                $messageSource = $event->sender;
                $db = $messageSource->db;

                // Extract first part of "en-EN" form
                $sourceLang = explode('-', $messageSource->sourceLanguage)[0];
                $targetLang = explode('-', $event->language)[0];
        
                $content = file_get_contents('https://translate.yandex.net/api/v1.5/tr.json/translate?' . http_build_query([
                        'key' => $this->apiKey,
                        'text' => $event->message,
                        'format' => 'plain',
                        'lang' => "$sourceLang-$targetLang",
                ]));
                if ($result = json_decode($content, true)) {
                        $event->translatedMessage = ArrayHelper::getValue($result, 'text.0');
                        if ($event->translatedMessage) {
                            // try to find message source
                            $id = (new Query())->select(['id'])
                                ->from($messageSource->sourceMessageTable)
                                ->where(['category' => $event->category, 'message' => $event->message])
                                ->createCommand()
                                ->queryScalar();
                            // if not found, insert a new one. Note: it's better to use "upsert" command (depending of the used DB engine)
                            if (!$id) {
                                $db->createCommand()->insert($messageSource->sourceMessageTable, [
                                        'category' => $event->category, 
                                        'message' => $event->message            
                                ])->execute();
                                $id = $db->getLastInsertId()
                            }
                            // insert new translated message.
                            $db->createCommand()->insert($messageSource->messageTable, [
                                    'id' => $id,
                                    'language' => $event->language,
                                    'translation' => $event->translatedMessage
                            ])->execute();
                        }
                }
                if (!$event->translatedMessage) {
                        $event->translatedMessage = "@MISSING: {$event->category}.{$event->message} FOR LANGUAGE {$event->language} @";
                }
                return null;
        }
}
```

Now we need to configure a new translator:

```php
'components' => [
    // ...
     'i18n' => [
        'translations' => [
            'machine' => [
                'class' => 'yii\i18n\DbMessageSource',
                'on missingTranslation' => [
                    new common\components\YandexTranslation([
                        'apiKey' => 'some api key',
                    ]), 
                    'handleMissingTranslation'
                ],
            ],
        ],
    ],
],
```

## How it works

To use machine translator put "machine" category in 't' function Yii::t('machine', 'Text to translate');
Do not forget to enable cache in production.
