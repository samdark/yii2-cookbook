Using machine translate (such as google or yandex) as translation source
================================

Sometimes we need to translate site to other languages.
I do not recommend to use a Machine translate as production ready solution, but it can helps to prepare site for interpreters.


## How to do it

First of all we need catch MissingTranslationEvent. We can wrap catch method in component.
We use DbMessageSource to store traslated messages. Do not forget to enable cache in production.

class MachineTranslation extends Component
{
    /**
	 * Provider api key.
	 */
    public $apiKey;
    
	public $db;
	
	public function init()
	{
		$this->db = \Yii::$app->db;
	}
	
    /**
     * 
     * @param MissingTranslationEvent $event
     */
    public function handleMissingTranslation(MissingTranslationEvent $event)
    {
        /* @var $messageSource \yii\i18n\DbMessageSource */
        $messageSource = $event->sender;
        
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
				$this->db->createCommand()->insert($messageSource->sourceMessageTable, [
					'category' => $event->category, 
					'message' => $event->message            
				])->execute();
				if ($id = $this->db->getLastInsertId()) {
					$this->db->createCommand()->insert($messageSource->messageTable, [
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
