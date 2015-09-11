Processing text
===============

When implementing post or article publishing, it's very important to choose right tool for the job. Common approach is to use WYSIWYG (what you see is what you get) editor that produces HTML but that has significant cons. The most prominent con is that it's easy to break website design and to produce excessive and ugly HTML. The pro is that it's quite natural for people worked with MS Word or alike text processors.

Luckily, we have simple markups such as markdown nowadays. While being very simple, it has everything to do basic text formatting: emphasis, hyperlinks, headers, tables, code blocks etc. For tricky cases it still accepts HTML.

Converting markdown to HTML
---------------------------

### How to do it

Markdown helper

### Where to do it

- Right in the view because it's fast
- Could save to separate field in DB (afterSave) or cache (afterSave) for extra performance (can't save to the same field because of the need to edit original)

### How to secure output

HTMLPurifier

Could wrap into a simple call.

Alternatives
------------

Markdown is not the only simple markup available:

- 
- 
- 
