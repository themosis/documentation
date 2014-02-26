PostType
========

The `PostType` class helps register/build WordPress custom post types.

**Note**: All the code in order to customize WordPress should be stored inside the `app/admin` directory of the `themosis-datas` plugin.

### Basic custom post type

```php

// File stored in app/admin/my-file.php

PostType::make('slug-books', 'Books')->set();

```

This will build a basic custom post type accessible in the WordPress admin. You can customize the custom post type by passing arguments to the `set()` method.

```php

PostType::make('slug-books', 'Books')->set(array(

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
PostType::make($slug, $name);
```

* `$slug`: _string_. The custom post type slug (we take the habit to give a plural noun as it represents a "collection").
* `$name`: _string_. The display name of your custom post type.

#### PostType->set()

```php
$postType = PostType::make('slug-books', 'Books');

$postType->set($args);
```

* `$args`: _array_. Array of arguments in order to customize the behavior of your custom post type. Check the [WordPress guide](http://codex.wordpress.org/Function_Reference/register_post_type) for the list of arguments you can pass to the set method. **Note**: The custom post type is not registered until you call the "set" method.

#### PostType->getSlug()

```php
$postType = PostType::make('slug-books', 'Books')->set();

// Code

$slug = $postType->getSlug();
```

Getter method that helps you retrieve the **slug** value of your custom post type. May be used when you're registering a custom taxonomy or a metabox.

Next
----
You can read the following documentation:

* [Metabox guide](https://github.com/themosis/documentation/blob/master/metabox.md)
* [Taxonomy guide](https://github.com/themosis/documentation/blob/master/taxonomy.md)