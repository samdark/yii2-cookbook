Using custom migration template
===============================

For many cases it's useful to customize code that's created when you run `./yii migrate/create`. A good example is migrations for MySQL InnoDB
where you need to specify engine.

## How to do it

First of all, copy standard template i.e. `framework/views/migration.php` to your app. For example, to `views/migration.php`.

Then customize template:

```php
<?php
/**
 * This view is used by console/controllers/MigrateController.php
 * The following variables are available in this view:
 */
/* @var $className string the new migration class name */

echo "<?php\n";
?>

use yii\db\Migration;

class <?= $className ?> extends Migration
{
    public function up()
    {
        $tableOptions = null;
        if ($this->db->driverName === 'mysql') {
            // http://stackoverflow.com/questions/766809/whats-the-difference-between-utf8-general-ci-and-utf8-unicode-ci
            $tableOptions = 'CHARACTER SET utf8 COLLATE utf8_unicode_ci ENGINE=InnoDB';
        }
        
        
    }

    public function down()
    {
        echo "<?= $className ?> cannot be reverted.\n";

        return false;
    }

    /*
    // Use safeUp/safeDown to run migration code within a transaction
    public function safeUp()
    {
    }

    public function safeDown()
    {
    }
    */
}
```

Now in your console application config, such as `config/console.php` specify new template to use:

```php
return [
...

    'controllerMap' => [
        'migrate' => [
            'class' => 'yii\console\controllers\MigrateController',
            'templateFile' => '@app/views/migration.php',
        ],
    ],
 
...
];
```

That's it. Now `./yii migrate/create` will use your template instead of standard one.
