Using machine translation (such as google or yandex) as translation source.
================================

Sometimes we need to translate the site into other languages.
I do not recommend to use machine translation as production ready solution, but it can help to prepare the site to the translation by professional translators.


## How to do it

First of all we need catch MissingTranslationEvent. It is more convenient to wrap catch method in component.
We use DbMessageSource to store traslated messages. It is necessary to perform the appropriate migration.

```php
class MachineTranslation extends Component
{
    /**
	 * Provider api key.
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
		
        $sourceLang = explode('-', $messageSource->sourceLanguage)[0];
        $targetLang = explode('-', $event->language)[0];
        
        $content = file_get_contents('https://translate.yandex.net/api/v1.5/tr.json/translate?' . http_build_query([
            'key' => $this->apiKey,
            'text' => $event->message,
            'format' => 'plain',
            'lang' => "$sourceLang-$targetLang",
        ]));
        if ($result = json_decode($content)) {
			$event->translatedMessage = isset($result['text'][0]) ? $result['text'][0] : null;
			if ($event->translatedMessage) {
				$db->createCommand()->insert($messageSource->sourceMessageTable, [
					'category' => $event->category, 
					'message' => $event->message            
				])->execute();
				if ($id = $db->getLastInsertId()) {
					$db->createCommand()->insert($messageSource->messageTable, [
						'id' => $id,
						'language' => $event->language,
						'translation' => $event->translatedMessage
					])->execute();
				}
			}
        }
        if (!$event->translatedMessage) {
            $event->translatedMessage = "@MISSING: {$event->category}.{$event->message} FOR LANGUAGE {$event->language} @";
        }
        return null;
    }
}
```

Now we need to configure new translator:

```php
'components' => [
    // ...
     'i18n' => [
		'translations' => [
			'machine' => [
				'class' => 'yii\i18n\DbMessageSource',
				'on missingTranslation' => [
					new MachineTranslation([
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
