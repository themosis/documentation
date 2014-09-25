Meta
====

The `Meta` class is an helper class to help you retrieve data of custom fields registered as post meta.

1. Meta class
2. Example

## 1. The Meta class

```php
Meta::get($id, $key = '', $single = true);
```

* **$id**: _int_. The post ID the field is registered to.
* **$key**: _string_. The custom field `$name`.
* **$single**: _boolean_. Simply return the value or within an array.

This class method is equivalent to the WordPress core function: get_post_meta(). The difference is that this one also auto-converts media paths if the request protocol is `https`.

## 2. Example

Let's first build a simple metabox with custom fields.

```php
Metabox::make('Details', 'post')->set(array(
	Field::text('name'),
	Field::checkboxes('channels', array('email', 'mail', 'morse'))
));
```

Then we want to retrieve the registered datas:

```php
// Should return a string
$name = Meta::get($post_id, 'name');

// Should return an array of one or more values
$channels = Meta::get($post_id, 'channels');
```

As you can see in this example, some custom fields return an array as a value. The fields that return an array value are:

* Field::checkboxes()
* Field::select() - If `$multiple` option is set to `true`.
* Field::infinite() - An array of arrays.

The `Field::checkbox()` returns a string value of either `on` or `off` only.