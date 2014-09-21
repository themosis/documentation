Metabox
=======

As the name suggests, the `Metabox` class helps you build custom WordPress metabox.

Before digging into the `Metabox` documentation, make sure to read the [Field class guide](http://framework.themosis.com/docs/field/).

1. Build a metabox
2. Retrieve datas

## 1. Build a metabox

```php
Metabox::make($title, $postType, array $options = array(), IRenderable $view = null)->set($fields);
```

* **$title**: _string_. The display title of the metabox.
* **$postType**: _string_. The post type slug to link the metabox with.
* **$options**: _array_. An array of options. You can set the `context` and `priority` properties of the metabox.
* **$view**: _IRenderable_. A view file. Allow you to customize the UI of your metabox using the Scout engine.

Example of a metabox containing one text field:

```php
Metabox::make('Infos', 'post', array(
	'context' 	=> 'normal',
	'priority'	=> 'high'
))->set(array(
	Field::text('author')
));
```

Like the `PostType` class, a metabox is not registered until you call its `set()` method.

```php
Metabox::make($title, $postType, $options)->set($fields);
```

* **$fields**: _array_. An array of custom fields. Check the [Field guide](http://framework.themosis.com/docs/field/).

### Custom post type metabox

Example of a metabox linked to a custom post type with a slug of `custom-books`:

```php
// Define fields
$fields = array(
	Field::checkbox('available'),
	Field::text('author'),
	Field::select('category', array('fantasy', 'romance', 'horror'), false, array('title' => 'Book category:')),
	Field::infinite('chapters', array(
		Field::text('title'),
		Field::textarea('content')
	))
);

// A metabox object is always returned in order to be able to chain methods.
Metabox::make('Book details', 'custom-books')->set($fields);
```

### Page metabox

Example of a metabox linked to a page.

```php
// You can define directly your fields inside the set() method.
Metabox::make('Informations', 'page')->set(array(
	Field::text('name'),
	Field::text('phone'),
	Field::textarea('address')
));
```

## 2. Retrieve datas

In order to retrieve the custom fields data, you can use the core function `get_post_meta()` or use the `Meta` class. Please refer to the [Meta guide](http://framework.themosis.com/docs/meta/).

Next
----

* [Meta guide](http://framework.themosis.com/docs/meta/)
* [Taxonomy guide](http://framework.themosis.com/docs/taxonomy/)
* [Page guide](http://framework.themosis.com/docs/page/)