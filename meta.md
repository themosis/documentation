Meta
====

The `Meta` class is an helper class to help you retrieve data of custom fields registered as post meta.

- [Basic usage](#basic-usage)

Basic usage
-----------

```php
Meta::get($id, $key = '', $single = true);
```

* **$id**: _int_. The post ID the field is registered to.
* **$key**: _string_. The custom field `$name`.
* **$single**: _boolean_. Simply return the value or within an array.

This class method is equivalent to the WordPress core function: get_post_meta().

For an example, let's first build a simple metabox with custom fields:

```php
Metabox::make('Details', 'post')->set([
	Field::text('name'),
	Field::checkbox('channels', ['email', 'mail', 'morse'])
]);
```

Then we want to retrieve the registered data:

```php
// Should return a string
$name = Meta::get($post_id, 'name');

// Should return an array of one or more values
$channels = Meta::get($post_id, 'channels');
```