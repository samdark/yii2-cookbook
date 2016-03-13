CSRF
================

Cross-site request Forgery (CSRF) is a typical web application type of vulnerability. It is based on 
user authentication on a trusted site. User can visited malicious site and this site can imitate user requests 
to the trusted site through web browser. For example a user password can be changed or some funds is transferred.

[General recommendation](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_%28CSRF%29_Prevention_Cheat_Sheet) to prevent
is to use Synchronizer Token Pattern. And Yii provides this solution by default.

You can easily try this adding a simple form to you view and try to submit it:
```
<form method="POST">
    <input type="submit" value="ok!">
</form>
```



For every request Yii generates a special unique token. And you have to send it with your request data.
So if you make a request Yii compare generated and received tokens. If they match Yii continue to handle request. 
If not or CSRF token field is absent an error is occurred:

```
Bad Request (#400)
Unable to verify your data submission. 
```


Malicious site doesn't know this token so can't imitate request and harm your security.

Let's try this form:
```
<form method="GET">
    <input type="submit" value="ok!">
</form>
```
No error is occurred. This is because CSRF protection works only for unsafe request methods (PUT, POST, DELETE).

Disabling CSRF protection
----------------

Yii Controller property $enableCsrfValidation is true by default. You can disable CSRF validation by setting:

```php
class MyController extends Controller
{
    public $enableCsrfValidation = false;
```

Or disable for special action:
```php
class MyController extends Controller
{
    public function beforeAction($action)
    {
        if (in_array($action->id, ['incoming'])) {
            $this->enableCsrfValidation = false;
        }
        return parent::beforeAction($action);
    }
```

In some cases this is helpful. See this recipe: [Handling incoming third party POST requests](incoming-post.md).
For every `ActiveForm` a hidden field including this token is added automatically and it is not necessary for you
to worry about. But situation is different if you create your own form.

Adding CSRF protection 
----------------

it is not a good idea to disable protection in order to get rid of 'annoying' errors.
It is very simple to provide CSRF token for your form.

Let's add a necessary hidden field to our sample form:
```
<form method="POST">
    <input id="form-token" type="hidden" name="<?=Yii::$app->request->csrfParam?>"
           value="<?=Yii::$app->request->csrfToken?>"/>
    <input type="submit" value="ok!">
</form>
```

Try to send this form and no error is occurred.


See also
--------

- [OWASP article about CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_%28CSRF%29_Prevention_Cheat_Sheet)
- [Handling incoming third party POST requests](incoming-post.md).
