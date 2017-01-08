Taxonomy
========

- [Basic usage](#basic-usage)
	- [Plural and single display names](#plural-and-single-display-names)
	- [Customize your taxonomy](#customize-your-taxonomy)
	- [Shared taxonomies](#shared-taxonomies)
- [Bind your taxonomy](#bind-your-taxonomy)
- [Term custom fields](#term-custom-fields)
    - [Sanitize custom fields](#sanitize-custom-fields)

Basic usage
-----------

The `Taxonomy` class, as the name suggests, helps you build custom taxonomies.

In order to register a custom taxonomy, you will call the `make` and `set` methods of the Taxonomy class.

For example, let's register a custom taxonomy in order to handle a list of authors for our books custom post type from previous examples. The custom taxonomy will have a slug of `authors`:

```php
Taxonomy::make('authors', 'books', 'Authors', 'Author')->set([
    'public' => true
]);
```

This will build a basic custom taxonomy accessible from the books custom post type. You can customize your taxonomy by passing arguments to the `set()` method. In the above code we specified that the custom taxonomy should be public. You can pass all the arguments defined in the WordPress core function [register_taxonomy](https://developer.wordpress.org/reference/functions/register_taxonomy/) like so:

```php
Taxonomy::make('authors', 'books', 'Authors', 'Author')->set([
    'public'       => true,
    'rewrite'      => false,
    'query_var'    => false,
    'hierarchical' => true
]);
```

> Note: the custom taxonomy is only registered if you call the `set()` method.

### Plural and single display names

The `make()` method needs a plural and singular display names as third and fourth parameters. Those parameters will pre-fill the labels property of your custom taxonomy.

```php
Taxonomy::make('authors', 'books', 'Authors', 'Author')->set();
```

In the above code sample, the custom taxonomy will have a plural display name of `Authors` and a singular display name of `Author`.

> Note: you can override the custom taxonomy `labels` property by adding it to the set method array.

### Customize your taxonomy

In order to define the behavior of your custom taxonomy, use the `set()` method and pass it an array of [parameters](https://developer.wordpress.org/reference/functions/register_taxonomy/#parameters) like so:

```php
Taxonomy::make('authors', 'books', 'Authors', 'Author')->set([
    'public'             => true,
    'show_in_nav_menus'  => false,
    'hierarchical'       => true,
    'show_tagcloud'      => false,
    'show_in_quick_edit' => false
]);
```

### Shared taxonomies

You can define a taxonomy that can be shared between multiple post types by passing an array of post type names like so:

```php
Taxonomy::make('authors', ['post', 'books'], 'Authors', 'Author')->set();
```

The above code register an `authors` taxonomy for both `post` and `books` post types.

Bind your taxonomy
------------------

Attach the post type to the taxonomy inside filter callback that run during `parse_request` or `pre_get_posts`.

You can use the `bind()` method which is a shortcut to the [register\_taxonomy\_for\_object\_type](https://developer.wordpress.org/reference/functions/register_taxonomy_for_object_type/) function.

Call the method on your taxonomy instance like so:

```php
$tax = Taxonomy::make('authors', ['post', 'books'], 'Authors', 'Author')->set();

// Bind the taxonomy to the 'post' and 'books' post types.
$tax->bind();
```

> You can also bind your taxonomy when registering your custom post type by adding the `taxonomies` argument. Check the [register\_post\_type](https://developer.wordpress.org/reference/functions/register_post_type/) function.

Term custom fields
------------------

You can define custom fields to taxonomy terms by using the `addFields()` method of the Taxonomy class.

You can call it from a taxonomy instance, which will automatically retrieve the correct taxonomy slug in order to associate the custom fields. Or you can call the method directly in order to add custom fields to core taxonomy terms for example.

#### Add fields to a custom taxonomy

Call the `addFields()` method to a custom taxonomy instance and pass it an array of fields to add:

```php
$authors = Taxonomy::make('authors', $books->get('name'), 'Authors', 'Author')->set();

$authors->addFields([
    Field::media('profile'),
    Field::text('website')
]);
```

#### Add fields to a core taxonomy

Call the `addFields()` method directly in order to add custom fields to core taxonomy terms:

```php
Taxonomy::addFields([
    Field::text('subtitle')
], 'category');
```

In order to add custom fields to core taxonomy terms, please provide the taxonomy slug as a second parameter.

The `addFields()` method returns a Taxonomy instance, so it is possible to chain methods.

### Sanitize custom fields

You can sanitize term custom fields values using the `sanitize()` method. Here is an example using our custom taxonomy of authors:

```php
$authors = Taxonomy::make('authors', $books->get('name'), 'Authors', 'Author')->set();

$authors->addFields([
    Field::media('profile'),
    Field::text('website')
]);

$authors->sanitize([
    'website' => ['url']
]);
```

Pass an array of sanitize rules to the `sanitize()` method. Check the [validation guide]({{url}}/validation) for a list available rules.

