Working with ActiveForm via JavaScript
======================================

PHP side of ActiveForm, which is usually more than enough for majority of projects,
[is described well in the official Yii 2.0 guide](http://www.yiiframework.com/doc-2.0/guide-input-forms.html). It is getting
a bit more tricky when it comes to advanced things such as adding or removing form fields dynamically or triggering individual
field validation using unusual conditions.

In this recipe you'll be introduced to ActiveForm JavaScript API.

Preparations
------------

We're going to use basic project template contact form for trying things out so [install it first](http://www.yiiframework.com/doc-2.0/guide-start-installation.html).

Triggering validation for individual form fields
------------------------------------------------

```javascript
$('#contact-form').yiiActiveForm('validateAttribute', 'contactform-name');
```

Trigger validation for the whole form
-------------------------------------

```javascript
$('#contact-form').yiiActiveForm('validate', true);
```

The second passed argument `true` forces validation of the whole form.

Using events
------------

```javascript
$('#contact-form').on('beforeSubmit', function (e) {
	if (!confirm("Everything is correct. Submit?")) {
		return false;
	}
	return true;
});
```

Available events are:

- [`beforeValidate`](https://github.com/yiisoft/yii2/blob/master/framework/assets/yii.activeForm.js#L39).
- [`afterValidate`](https://github.com/yiisoft/yii2/blob/master/framework/assets/yii.activeForm.js#L50).
- [`beforeValidateAttribute`](https://github.com/yiisoft/yii2/blob/master/framework/assets/yii.activeForm.js#L64).
- [`afterValidateAttribute`](https://github.com/yiisoft/yii2/blob/master/framework/assets/yii.activeForm.js#L74).
- [`beforeSubmit`](https://github.com/yiisoft/yii2/blob/master/framework/assets/yii.activeForm.js#L83).
- [`ajaxBeforeSend`](https://github.com/yiisoft/yii2/blob/master/framework/assets/yii.activeForm.js#L93).
- [`ajaxComplete`](https://github.com/yiisoft/yii2/blob/master/framework/assets/yii.activeForm.js#L103).
- [`afterInit`](https://github.com/yiisoft/yii2/blob/master/framework/assets/yii.activeForm.js#L111).

Adding and removing fields dynamically
--------------------------------------

To add a field to validation list:

```javascript
$('#contact-form').yiiActiveForm('add', {
    id: 'address',
    name: 'address',
    container: '.field-address',
    input: '#address',
    error: '.help-block',
    validate:  function (attribute, value, messages, deferred, $form) {
        yii.validation.required(value, messages, {message: "Validation Message Here"});
    }
});
```

To remove a field so it's not validated:

```javascript
$('#contact-form').yiiActiveForm('remove', 'address');
```

Updating error of a single attribute
------------------------------------

In order to add error to the attribute:

```javascript
$('#contact-form').yiiActiveForm('updateAttribute', 'contactform-subject', ["I have an error..."]);
```

In order to remove it:

```javascript
$('#contact-form').yiiActiveForm('updateAttribute', 'contactform-subject', '');
```

Update error messages and, optionally, summary
----------------------------------------------

```javascript
$('#contact-form').yiiActiveForm('updateMessages', {
    'contactform-subject': ['Really?'],
    'contactform-email': ['I don\'t like it!']
}, true);
```

The last argument in the above code indicates if we need to update summary.

Listening for attribute changes
--------------------------------
To attach events to attribute changes like Select, Radio Buttons, etc.. you can use the following code

```javascript
$("#attribute-id").on('change.yii',function(){
        //your code here
});
```

Getting Attribute Value
-----------------------

In order to be compatible with third party widgets like (Kartik), the best option to retrieve the actual value of an attribute is:

```javascript
$('#form_id').yiiActiveForm('find', 'attribute').value
```

Custom Validation
-----------------

In case you want to change the validation of an attribute in JS based on a new condition, you can do it with the rule property whenClient, but in the case you need a validation that doesn't depends on rules (only client side), you can try this:

```javascript
$('#form_id').on('beforeValidate', function (e) {
            $('#form_id').yiiActiveForm('find', 'attribute').validate = function (attribute, value, messages, deferred, $form) {
                //Custom Validation
            }
        return true;
    });
```

AJAX form submission with server validation
-----------------

To submit form via AJAX it is required to attach a handler on `beforeSubmit` event. Handler will replace default form submission 
and is responsible for sending data to the server and displaying error messages if server validation fails. Displaying validation
messages requires support at the controller side.

Attach a handler to form: 

```js
$('#contact-form').on('beforeSubmit', function () {
    var $yiiform = $(this);
    $.ajax({
            type: $yiiform.attr('method'),
            url: $yiiform.attr('action'),
            data: $yiiform.serializeArray(),
        }
    )
        .done(function(data) {
            if(data.success) {
                // data is saved
            } else if (data.validation) {
                // server validation failed
                $yiiform.yiiActiveForm('updateMessages', data.validation, true); // renders validation messages at appropriate places
            } else {
                // incorrect server response
            }
        })
        .fail(function () {
            // request failed
        })

    return false; // prevent default form submission
})
```

Controller support:

```php
public function actionUpdate($id)
{
    $model = $this->findModel($id);

    if (Yii::$app->request->isAjax) {
        if ($model->load(Yii::$app->request->post()) && $model->save()) {
            return $this->asJson(['success' => true]);
        }

        $result = [];
        // The code below comes from ActiveForm::validate(). We do not need to validate the model
        // again, as it was already validated by save(). Just collect the messages.
        foreach ($model->getErrors() as $attribute => $errors) {
            $result[yii\helpers\Html::getInputId($model, $attribute)] = $errors;
        }

        return $this->asJson(['validation' => $result]);
    }

    return $this->render('update', [
        'model' => $model,
    ]);
}
```
