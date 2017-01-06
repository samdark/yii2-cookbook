# Using machine translation as translation source

Sometimes we need to translate the site into other languages but do not really know the language. In this case machine tranlation such as Google or Yandex may be helpful. At least for testing purposes. It is not recommended to use machine translation as production ready solution, but it can help to test UI and prepare project for more professional translation.


## How to do it

As an example we'll implement machine translation via Yandex APIs. We need respond to `MissingTranslationEvent`. It is more convenient to extract event handler method in a component. To store translated messages we'll use `DbMessageSource` thus [appropriate database migration should be applied](http://www.yiiframework.com/doc-2.0/yii-i18n-dbmessagesource.html).

Create `YandexTranslation` component:

```php
namespace common\components;

use yii\db\Query;
use yii\helpers\ArrayHelper;
use yii\i18n\MissingTranslationEvent;

class YandexTranslation extends \yii\base\Component
{
        /**
         * Provider API key. Get it for free at
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

To use machine translator put `machine` category in `t` function: `Yii::t('machine', 'Text to translate')`.
Do not forget to enable cache in production.
