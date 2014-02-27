Metabox
=======

As the name suggests, the `Metabox` class helps you build custom WordPress metabox.

Before digging into the `Metabox` documentation, make sure to read the [Field class guide](https://github.com/themosis/documentation/blob/master/field.md).

### Build a metabox

```php
Metabox::make($title, $postType, $options = array())->set($fields);
```

* `$title`: _string_. The display title of the metabox.
* `$postType`: _string_. The post type slug to link the metabox with.
* `$options`: _array_. An array of options. You can set the `context` and `priority` properties of the metabox.

Example of a metabox containing one text field:

```php
Metabox::make('Infos', 'post', array(

	'context' 	=> 'normal',
	'priority'	=> 'high'

))->set(array(

	Field::text('author')

));
```

Like for the `PostType` class, a metabox is not registered until you call its `set()` method.

```php
Metabox::make($title, $postType, $options)->set($fields);
```

* `$fields`: _array_. An array of custom fields. Check the [Field guide](https://github.com/themosis/documentation/blob/master/field.md).

### Custom post type metabox

Example of a metabox link to a custom post type with a slug of `custom-books`:

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

Example of a metabox link to a page.

```php
// You can define directly your fields inside the set() method.
Metabox::make('Informations', 'page')->set(array(

	Field::text('name'),
	Field::text('phone'),
	Field::textarea('address')

));
```

Next
----

* [Taxonomy guide](https://github.com/themosis/documentation/blob/master/taxonomy.md)
* [Page guide](https://github.com/themosis/documentation/blob/master/page.md)