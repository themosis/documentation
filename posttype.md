PostType
========

The `PostType` class helps register/build WordPress custom post types.

**Note**: All the code in order to customize WordPress should be stored inside the `app/admin` directory of the `themosis-datas` plugin.

### Basic custom post type

```php

// File stored in app/admin/my-file.php

PostType::make('book-slug', 'Books')->set();

```

This will build a basic custom post type accessible in the WordPress admin. You can customize the custom post type by passing arguments to the `set()` method.

```php

PostType::make('book-slug', 'Books')->set(array(

	'public'        => true,
    'menu_position' => 20,
    'supports'      => array('title'),
    'rewrite'       => false,
    'query_var'     => false

));

```

> The arguments you pass are the same used by the WordPress [register_post_type](http://codex.wordpress.org/Function_Reference/register_post_type) function.