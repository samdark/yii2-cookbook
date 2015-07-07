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
$('#contact-form').data('yiiActiveForm').submitting = true;
$('#contact-form').yiiActiveForm('validate');
```

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

Adding and removing fields dynamically
--------------------------------------

```javascript
$('#contact-form').yiiActiveForm('add', {
    'id': 'address',
    'name': 'address',
    'container': '.field-address',
    'input': '#address',
    'error': '.field-address .help-block'
});
```

