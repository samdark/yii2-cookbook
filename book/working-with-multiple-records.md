Working With Multiple Records
=============================

Sometimes one request can create or affect several records in one or more tables
in such cases its advisable to follow [ACID](https://en.wikipedia.org/wiki/ACID)
properties. Yii2 supports transactions, isolation levels, and allow you to
validate data independently.

For example when creating a credit, you also need to store the credit references
and files associated to the credit.

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
            $valid =  Model::::validateMultiple($modelFiles, [
                    'file',
                    // other fields, make sure to NOT include credit_id since
                    // the record has not been created yet.
                ]) && $valid;

            if ($valid) {
                // the model was validated, no need to validate it once more
                $model->save(false);

                foreach ($modelReferences as $newModel) {
                    $newModel->credit_id = $model->id;
                    $newModel->save(false);
                }

                foreach ($modelFiles as $newModel) {
                    $newModel->credit_id = $model->id;
                    $newModel->file->saveAs(Yii::getAlias(
                        '@webroot/uploads/' . $model->file->name
                    ));
                    $newModel->save(false);
                }

                $transaction->commit();
                return $this->redirect(['credit/view', ['id' => $model->id]]);
            } else {
                $transaction->rollBack();
            }
        } catch (Exception $e) {
            $transaction->rollBack();
            throw new BadRequestHttpException($e->getMessage(), 0, $e);
        }
    }

    return $this->render('create', [
        'model' => $model,
        'modelReferences' => $modelReferences,
        'modelFiles' => $modelFiles,
    
    ]);
}
```

The steps followed in the example.

1 Check if the request can process the petition.

In this case we are assuming the controller already checked the user credentials
using filter and at the action its enough to check if the petition is using the
`post` method.

2 Create the models and load the user data to them

While there is only one credit, there might be many files and references

3 Start the transaction

Its important to start the transaction at this point since some validations like
`unique` and `exist` might be necessary so we start the transaction here to
avoid [Reading Phenomena]
(https://en.wikipedia.org/wiki/Isolation_\(database_systems\)#Read_phenomena).

You should also notice that we created the transaction using
`yii\db\Transaction::SERIALIZABLE` which is the highest [isolation level]
(https://en.wikipedia.org/wiki/Isolation_\(database_systems\)#Isolation_levels).

4 Validate all the models

Its important to validate them all to show the user all the validation errors if
necessary. Using an `if` statement like this

```php
if ($model->validate() && Model::validateMultiple(...))
```

Is simpler to understand but if the first validation fails the second one won't
be executed.

Also notice that we are not going to validate `credit_id` on the files and
references since the credit has not been created yet.

4.1 if the validations fail, end the transaction with a `rollBack()` just in
case any validation had updated anything

The action will then render the view and if you are using something like
`ActiveForm` the user will see all the validation errors.

5 if the all the validations are successful we proceed to save all the models.

We will save them without validation since they were already validated and
assign the `credit_id` to the files and references after the credit has been
saved.

5.1 Catch any exception from the validation or saving and execute `rollBack()`

For debugging purposes we throw a new exception with the previous one so it can 
get caught by the Yii2 exception manager.

6 If no exception is thrown then `commit()` the changes.

After this you can include any success logic like redirects or new renders.

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
            throw new Exception("credit couldn't be update");
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
        self::SCENARIO_DEFAULT => self::OP_INSERT,
    ];
}
```
