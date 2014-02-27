Taxonomy
========

The `Taxonomy` class, as the name suggests, helps you build custom taxonomies.

```php
Taxonomy::make($slug, $name, $postType)->set($params);
```

* `$slug`: _string_. The taxonomy slug name.
* `$name`: _string_. The taxonomy display name (label).
* `$postType`: _string_. The post type slug to associate the taxonomy with.

Like in other classes, the `Taxonomy` object uses a `set()` method to be registered.

* `$params`: _array_. An array of parameters. Same as in the WordPress core function `register_taxonomy`. Check the [codex page](http://codex.wordpress.org/Function_Reference/register_taxonomy) in order to customize your taxonomy.

### Simple custom taxonomy

```php
Taxonomy::make('services', 'Services', 'projects')->set();
```

This builds a `Services` taxonomy for the `projects` custom post type.

### Custom taxonomy with parameters

```php
Taxonomy::make('brands', 'Brands', 'clothes')->set(array(
    'public'		=> true,
	'rewrite'		=> false,
	'query_var'		=> false,
	'hierarchical'	=> true
));
```

This builds a `Brands` taxonomy for the `clothes` custom post type.
