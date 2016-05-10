Working With Multiple Records
=============================

Sometimes one request can create or affect several records in one or more tables
in such cases its advisable to follow [ACID](https://en.wikipedia.org/wiki/ACID)
rules. Yii2 supports transactions, isolation levels, and allow you to
validate data independently, so following the ACID rules is simple.

For example when creating a credit, you also need to store the credit references
and file associated to it.

```php
public function actionCreate()
{
    $model = new Credit();
    $modelReferences = [new CreditRefence()];
    $modelFiles = [new CreditFile()];

    if (Yii::$app->request->isPost) {
        $model->load(Yii::$app->request->post());

        foreach (Yii::$app->requestPost($modelReferences[0]->formName())
            as $i => $data
        ) {
            $newModel = new CreditReference();
            $newModel->load($data, '');
            $modelReferences[$i] = $newModel;
        }

        foreach (Yii::$app->requestPost($modelFiles[0]->formName())
            as $i => $data
        ) {
            $newModel = new CreditFile();
            $newModel->load($data, '');
            $newModel->file = UploadedFile::getInstance($newModel, "[$i]file");
            $modelFiles[$i] = $newModel;
        }

        $transaction = Yii::$app->db->beginTransaction(
            Transaction::SERIALIZABLE
        );

        try {
            $valid = $model->validate();
            $valid = Model::validateMultiple($modelReferences, [
                    'name',
                    'last_name',
                    // other fields, make sure to NOT include credit_id since
                    // the record has not been created yet.
                ]) && $valid;
            if ($model->validate()
                && ActiveRecord::validateMultiple($modelReferences, [
                    'name',
                    'last_name',
                    // other fields, make sure to NOT include credit_id since
                    // the record has not been created yet.
                ])
                && ActiveRecord::validateMultiple($modelFiles, [
                    'file',
                    // other fields, make sure to NOT include credit_id since
                    // the record has not been created yet.
                ])
            ) {
                // the model was validated, no need to validate it once more
                $model->save(false);

                foreach ($modelReferences as $newModel) {
                    $newModel->credit_id = $model->id;
                    $newModel->save(false);
                }

                foreach ($modelFiles as $newModel) {
                    $newModel->credit_id = $model->id;
                    $newModel->file->saveAs(
                        '@web/uploads/' . $model->file->name
                    );
                    $newModel->save(false);
                }

                $transaction->commit();
                return $this->redirect(['credit/view', ['id' => $model->id]]);
            } else {
                $transaction->rollBack();
            }
        } catch (Exception $e) {
            $transaction->rollBack();
            throw $e;
        }
    }

    return $this->render('create', [
        'model' => $model,
        'modelReferences' => $modelReferences,
        'modelFiles' => $modelFiles,
    
    ]);
}

The steps followed in the example.

1 Check if the request can process the petition

Operations Triggered by Events
------------------------------

Lets say you have two models `Credit` with attributes `id` and `amount`, and a
`CreditPayment` model with attributes `credit_id` and `amount`. You want to
update the `Credit` amount when a related `CreditPayment` is created.

```php

class CreditPayment extends ActiveRecord
{
    public function afterSave($insert, $changedAttributes)
    {
        parent::afterSave($insert, $changedAttributes);
        if ($insert === false) {
            return; // only work with newly created payments
        }

        $this->credit->amount = $this->credit->amount - $this->ammount;
        if ($this->credit->amount <= 0) {
            $this->credit->status = Credit::STATUS_PAYMENT_FINISHED;
        }

        if ($this->credit->save(false) === false) {
            throw new Exception('credit couldn't be update');
        }
    }

    public function getCredit()
    {
        return $this->hasOne(Credit::className(), ['id' => 'credit_id']);
    }
}
```

If any exception is thrown in this method, then the payment will be
saved but it won't affect the amount on the credit.

We can encapsulate all the operation on a transaction very simply using the
method `yii\db\ActiveRecord::transactions()`

```php
public function transactions()
{
    return [
        self::SCENARIO_DEFAULT => OP_INSERT,
    ];
}
```
