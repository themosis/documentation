Taxonomy
========

The `Taxonomy` class, as the name suggests, helps you build custom taxonomies.

```php
Taxonomy::make($slug, $postType, $plural, $singular)->set($params);
```

* **$slug**: _string_ The taxonomy slug name.
* **$postType**: _string_ The post type slug to associate the taxonomy with.
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

This builds a `Services` taxonomy for the `projects` custom post type.

### Custom taxonomy with parameters

```php
Taxonomy::make('brands', 'clothes', 'Brands', 'Brand')->set(array(
    'public'		=> true,
	'rewrite'		=> false,
	'query_var'		=> false,
	'hierarchical'	=> true
));
```

This builds a `Brands` taxonomy for the `clothes` custom post type.
