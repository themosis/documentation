Taxonomy
========

- [Basic usage](#basic-usage)
- [Labels](#labels)
- [Customize your taxonomy](#customize-your-taxonomy)
- [Shared taxonomies](#shared-taxonomies)

Basic usage
-----------

The `Taxonomy` class, as the name suggests, helps you build custom taxonomies.

> Taxonomy terms meta fields are no longer supported in version 2.0 of the framework.

In order to register a custom taxonomy, you may call the `make` and `set` methods of the Taxonomy class.

For example, let's register a custom taxonomy in order to handle a list of authors for our books custom post type from previous examples. The custom taxonomy will have a slug of `authors`:

```php
use Themosis\Support\Facades\Taxonomy;

Taxonomy::make('authors', 'books', 'Authors', 'Author')
    ->set();
```
> Note: the custom taxonomy is only registered when you call the `set()` method and is automatically bound to attached objects.

Labels
------

The `make()` method needs a plural and singular display names as third and fourth parameters. Those parameters will pre-fill the labels property of your custom taxonomy.

```php
Taxonomy::make('authors', 'books', 'Authors', 'Author')->set();
```

In the above code sample, the custom taxonomy will have a plural display name of `Authors` and a singular display name of `Author`.

If you need to define the taxonomy labels, you can override the default taxonomy labels by calling the `setLabels` method on your taxonomy instance like so:

```php
Taxonomy::make('authors', 'books', 'Authors', 'Author')
    ->setLabels([
        'edit_item' => 'Edit Me'
    ])
    ->set();
```

  > See the [get_taxonomy_labels()](https://developer.wordpress.org/reference/functions/get_taxonomy_labels/) function for a full list of available labels.

Customize your taxonomy
-----------------------

In order to define the behavior of your custom taxonomy, use the `setArguments()` method and pass it an array of [parameters](https://developer.wordpress.org/reference/functions/register_taxonomy/#parameters) like so:

```php
Taxonomy::make('authors', 'books', 'Authors', 'Author')
    ->setArguments([
        'public' => true,
        'show_in_nav_menus'  => false,
        'hierarchical' => true,
        'show_tagcloud' => false,
        'show_in_quick_edit' => false
    ])
    ->set();
```

Shared taxonomies
-----------------

You can define a taxonomy that can be shared between multiple post types by passing an array of post type names like so:

```php
Taxonomy::make('authors', ['post', 'books'], 'Authors', 'Author')->set();
```

The above code register an `authors` taxonomy for both `post` and `books` post types.