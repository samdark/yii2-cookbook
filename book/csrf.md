CSRF
====

Cross-site request Forgery (CSRF) is one of a typical web application vulnerabilities. It's based on the assumption that
user may be authenticated at some legitimate website. Then he's visiting attacker's website which issues requests to
legitimate website using JavaScript code, a form, `<img src="` tag or any other means. This way attacker could, for
example, reset victim's password or transfer funds from his bank account (in case bank website isn't secure, of course).

[One of the ways](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_%28CSRF%29_Prevention_Cheat_Sheet)
to prevent this type of attacks is to use a special token to validate request origin. Yii does it by default.
You can try it by adding a simple form to you view and submitting it:

```
<form method="POST">
    <input type="submit" value="ok!">
</form>
```

You'll get the following error:

```
Bad Request (#400)
Unable to verify your data submission. 
```

This is because for every request Yii generates a special unique token that you have to send with your request data.
So when you make a request Yii compares generated and received tokens. If they match Yii continues to handle request.
If they don't match or in case CSRF token is missing in request data, an error is raised.

The token generated isn't known to attacker website so it can't make requests on your behalf.

An important thing to note is about request methods such as `GET`. Let's add the following form and submit it:

```
<form method="GET">
    <input type="submit" value="ok!">
</form>
```

No error has occurred. This is because CSRF protection works only for unsafe request methods such `PUT`, `POST` or
`DELETE`. That's why in order to stay safe your application should never ever use `GET` requests to change application
state.

## Disabling CSRF protection

In some cases you may need to disable CSRF validation. In order to do it set `$enableCsrfValidation` Controller property
to `false`:

```php
class MyController extends Controller
{
    public $enableCsrfValidation = false;
```

In order to do the same for a certain action use the following code:

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

See [handling incoming third party POST requests](incoming-post.md) for details.

## Adding CSRF protection

CSRF protection is enabled by default so what you need is to submit a token along with all your requests. Usually it's
done via hidden field:

```
<form method="POST">
    <input id="form-token" type="hidden" name="<?=Yii::$app->request->csrfParam?>"
           value="<?=Yii::$app->request->csrfToken?>"/>
    <input type="submit" value="ok!">
</form>
```

In case `ActiveForm` is used, token is added automatically.

## See also

- [OWASP article about CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_%28CSRF%29_Prevention_Cheat_Sheet)
- [Handling incoming third party POST requests](incoming-post.md).
