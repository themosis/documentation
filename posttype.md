PostType
========

The `PostType` class helps you to register WordPress custom post types.

**Note**: All the code in order to customize WordPress should be stored inside the `app/admin` directory.

### Basic custom post type

```php
// File stored in app/admin/any-file.php

PostType::make('slug-books', 'Books', 'Book')->set();

```

This will build a basic custom post type accessible in the WordPress admin. You can customize the custom post type by passing arguments to the `set()` method.

```php
PostType::make('slug-books', 'Books', 'Book')->set(array(

	'public'        => true,
    'menu_position' => 20,
    'supports'      => array('title'),
    'rewrite'       => false,
    'query_var'     => false

));

```

> The arguments you pass are the same used by the WordPress [register_post_type](http://codex.wordpress.org/Function_Reference/register_post_type) function.

### Methods:
#### PostType::make()
_public static method_

```php
PostType::make($slug, $plural-name, $singular-name);
```

* **$slug**: _string_ The custom post type slug (we take the habit to give a plural noun as it represents a "collection").
* **$plural-name**: _string_ The plural name of your custom post type.
* **$singular-name**: _string_ The singular name of your custom post type.

#### PostType->set()

```php
$postType = PostType::make('slug-books', 'Books', 'Book');

$postType->set($args);
```

* **$args**: _array_ Array of arguments in order to customize the behavior of your custom post type. Check the [WordPress Codex](http://codex.wordpress.org/Function_Reference/register_post_type) for the list of arguments you can pass to the set method.
 
**Note**: The custom post type is not registered until you call the **set()** method.

#### PostType->getSlug()

```php
$postType = PostType::make('slug-books', 'Books', 'Book')->set();

// Code

$slug = $postType->getSlug();
```

Getter method that helps you retrieve the **slug** value of your custom post type. May be used when you're registering a custom taxonomy or a metabox.

Next
----
You can read the following documentation:

* [Metabox guide](http://framework.themosis.com/docs/metabox/)
* [Taxonomy guide](http://framework.themosis.com/docs/taxonomy/)