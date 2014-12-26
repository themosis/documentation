Metabox
=======

As the name suggests, the `Metabox` class helps you build custom WordPress metabox.

Before digging into the `Metabox` documentation, make sure to read the [Field class guide](http://framework.themosis.com/docs/field/).

1. Build a metabox
2. Retrieve datas
3. Customize a metabox

1.Build a metabox
-----------------

```php
Metabox::make($title, $postType, $options = array(), $view = null)->set($fields);
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
	Field::select('category', array('fantasy', 'romance', 'horror'), false, array(
            'title' => 'Book category:')),
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

### Validate the custom fields

The class gives you a method to validate your custom fields. Use the `validate()` method like so:

```php
$metabox = Metabox::make('A title', 'page')->set(array(
	Field::text('email'),
	Field::text('name'),
	Field::text('phone'),
	Field::textarea('address'),
	Field::infinite('team', array(
		Field::text('name'),
		Field::text('age')
	))
));

// Let's validate our custom fields
$metabox->validate(array(
	'email'		=> array('email'),
	'name'		=> array('textfield', 'min:3', 'alpha'),
	'phone'		=> array('num', 'max:25'),
	'address'	=> array('textarea'),
	'team'		=> array(
		'name'		=> array('textfield', 'alpha', 'min:3', 'max:50'),
		'age'		=> array('num')
	)
));
```
Note how the `infinite` field is validated.

> If validation passes, the value entered is registered. In case the validation fails, it returns an empty string value.

Check the [validation guide](http://framework.themosis.com/docs/validation/) for more information about the validation rules.

2.Retrieve datas
----------------

In order to retrieve the custom fields data, you can use the core function `get_post_meta()` or use the `Meta` class. Please refer to the [Meta guide](http://framework.themosis.com/docs/meta/).

3.Customize the metabox
-----------------------

You can customize the look and feel and the behaviour of your metabox by defining a custom view (and why not view composers...).

You can pass a custom view for your metabox using the 4th argument of the `Metabox::make()` method like so:

```php
// Code below written inside the 'admin/metabox.php' file.
// File stored inside the 'views/metabox/custom.scout.php'.
$view = View::make('metabox.custom');

Metabox::make('Properties', 'post', array('priority' => high), $view);
```

Inside your view file for your metabox you have access to "special" data by default:

- **$__fields**: This variable gives you an array of registered fields with your metabox.
- **$__metabox**: This variable gives you access to your metabox instance.
- **$__post**: This variable gives you access to the current post instance object (WP_Post).

This allows you to customize as you want the look of your metabox. By also using `View::composer()` method, you might also perform specific actions when the metabox is rendered.

In case you needed to customize the metabox output but still need to output the core custom fields, simply add this code snippet inside your metabox view:

```php
<!-- Default Themosis metabox view -->
<table class="form-table themosis-metabox">
    <tbody>
        @each('_themosisMetaboxRow', $__fields, 'field')
    </tbody>
</table>
```

Next
----

* [Meta guide](http://framework.themosis.com/docs/meta/)
* [Taxonomy guide](http://framework.themosis.com/docs/taxonomy/)
* [Page guide](http://framework.themosis.com/docs/page/)