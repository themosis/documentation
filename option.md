Option
======

The `Option` class helps you retrieve options/settings data.

1. Option class
2. Example

1.The Option class methods
--------------------------

The `Option` class has only one method. The `get` method helps you retrieve your option value.

### get($optionGroup, $name)

```php
Option::get($optionGroup, $name);
```

* `$optionGroup`: _string_. The option group slug name.
* `$name`: _string_. The setting name.

> The option group value depends on how you save your settings with the `Page` class. If you use default section with tabs, the option group value is the section name slug. If you use section without tabs, the option group value is the page name slug.

> If no value/setting is found, the value returned is an empty string.

Next
----

Discover the other features of the framework:

* [Ajax](http://framework.themosis.com/docs/ajax/)
* [Asset](http://framework.themosis.com/docs/asset/)
* [PostType](http://framework.themosis.com/docs/posttype/)
* [Field](http://framework.themosis.com/docs/field/)
* [Metabox](http://framework.themosis.com/docs/metabox/)
* [Taxonomy](http://framework.themosis.com/docs/taxonomy/)
* [Page](http://framework.themosis.com/docs/page/)
* [Helpers](http://framework.themosis.com/docs/helpers/)

