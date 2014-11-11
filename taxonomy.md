Taxonomy
========

1. Register a taxonomy
2. Bind a taxonomy

1.Register a taxonomy
---------------------

The `Taxonomy` class, as the name suggests, helps you build custom taxonomies.

```php
Taxonomy::make($slug, $postType, $plural, $singular)->set($params);
```

* **$slug**: _string_ The taxonomy slug name.
* **$postType**: _string_|_array_ The post type slug(s) to associate the taxonomy with. Use an array to associate the taxonomy to multiple post types.
* **$plural**: _string_ The plural display name.
* **$singular**: _string_ The singular display name.

Like in other classes, the `Taxonomy` object uses a `set()` method to register.

```php
Taxonomy::make($slug)->set($params = array());
```

* **$params**: _array_. An array of parameters. Same as in the WordPress core function `register_taxonomy`. Check the [codex page](http://codex.wordpress.org/Function_Reference/register_taxonomy) in order to customize your taxonomy.

### Simple custom taxonomy

```php
Taxonomy::make('services', 'projects', 'Services', 'Service')->set();
```

This builds a `services` taxonomy for the `projects` custom post type.

### Custom taxonomy with parameters

```php
Taxonomy::make('brands', 'clothes', 'Brands', 'Brand')->set(array(
    'public'		=> true,
	'rewrite'		=> false,
	'query_var'		=> false,
	'hierarchical'	=> true
));
```

This builds a `brands` taxonomy for the `clothes` custom post type.

### Associate a taxonomy to multiple post types

```php
Taxonomy::make('authors', array('post', 'books'), 'Authors', 'Author')->set();
```

This creates an `authors` taxonomy for `post` and `books` post types.

2.Bind a taxonomy
-----------------

Attach the post type to the taxonomy inside filter callback that run during `parse_request` or `pre_get_posts`.

You can use the `bind()` method which is a shortcut to the [register\_taxonomy\_for\_object\_type](http://codex.wordpress.org/Function_Reference/register_taxonomy_for_object_type) function.

Call the method on your taxonomy instance like so:

```php
$tax = Taxonomy::make('authors', array('post', 'books'), 'Authors', 'Author')->set();

// Bind the taxonomy to the 'post' and 'books' post types.
$tax->bind();
```

> You can also bind your taxonomy when registering your custom post type by adding the `taxonomies` argument. Check the [register\_post\_type](http://codex.wordpress.org/Function_Reference/register_post_type).