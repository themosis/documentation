Option
======

- [Basic usage](#basic-usage)

The `Option` class helps you retrieve options/settings data.

Basic usage
-----------

The `Option` class has only one method. The `get` method helps you retrieve your option value.

```php
Option::get($optionGroup, $name);
```

* `$optionGroup`: _string_. The option group slug name.
* `$name`: _string_. The setting name.

The option group value depends on how you save your settings with the `Page` class. If you use sections with tabs, the option group value is the section name slug. If you use sections without tabs, the option group value is the page name slug.

> If no value/setting is found, the value returned is an empty string.

Next
----

Discover the other features of the framework:

* [Ajax]({{url}}/ajax)
* [Asset]({{url}}/asset)
* [PostType]({{url}}/posttype)
* [Field]({{url}}/field)
* [Metabox]({{url}}/metabox)
* [Taxonomy]({{url}}/taxonomy)
* [Page]({{url}}/page)
* [Helpers]({{url}}/helpers)