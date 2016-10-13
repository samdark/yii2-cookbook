Structure: backend and frontend via modules
===========================================

https://github.com/yiisoft/yii2/issues/3647

By default Yii comes with advanced application template that allows you to properly structure backend and frontend.
It's a good way to deal with the problem except when you want to redistribute parts of your application. In this
case a better way is to use modules.

Directory structure
-------------------

```
common
  components
  models
backend
  controllers
  views
  Module
frontend
  controllers
  views
  Module
```

Namespaces
----------

Root namespace is the same as in any extension i.e. `samdark\blog` (PSR-4 record required in `composer.json`).
Common stuff is under `samdark\blog\common`. Backend module is `samdark\blog\backend\Module`, frontend module
is `samdark\blog\frontend\Module`.

Using it
--------

- Install via Composer.
- In your application config use modules:

```php
'modules' => [
  'blogFrontend' => [
    'class' => 'samdark\blog\frontend\Module',
    'anonymousComments' => false,
  ],
  'blogBackend' => [
    'class' => 'samdark\blog\backend\Module',
  ],
]
```

- Access via browser:

```
http://example.com/blog-frontend/post/view?id=10
http://example.com/blog-backend/user/index
```
