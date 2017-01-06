Processing text
===============

When implementing post or article publishing, it's very important to choose right tool for the job. Common approach is to use WYSIWYG (what you see is what you get) editor that produces HTML but that has significant cons. The most prominent con is that it's easy to break website design and to produce excessive and ugly HTML. The pro is that it's quite natural for people worked with MS Word or alike text processors.

Luckily, we have simple markups such as markdown nowadays. While being very simple, it has everything to do basic text formatting: emphasis, hyperlinks, headers, tables, code blocks etc. For tricky cases it still accepts HTML.

Converting markdown to HTML
---------------------------

### How to do it

Markdown helper is very easy to use:

```php
$myHtml = Markdown::process($myText); // use original markdown flavor
$myHtml = Markdown::process($myText, 'gfm'); // use github flavored markdown
$myHtml = Markdown::process($myText, 'extra'); // use markdown extra
```

### How to secure output

Since markdown allows pure HTML as well, it's not secure to use it as is. Thus we'll need to post-process output via HTMLPurifier:

```php
$safeHtml = HtmlPurifier::process($unsafeHtml);
```

### Where to do it

- The library used to convert markdown to HTML is fast so processing right in the view could be OK.
- Result could be saved into separate field in database or cached for extra performance. Both could be done in `afterSave` method of the model. Note that in case of database field we can't save processed HTML to the same field because of the need to edit original.

Alternatives
------------

Markdown is not the only simple markup available. A good [overview exists in Wikipedia](https://en.wikipedia.org/wiki/Lightweight_markup_language).
