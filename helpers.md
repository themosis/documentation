Helpers
=======

Helpers are framework utility functions you can use anywhere. They are functions that run on the global scope.

## td($value)

This function prints a `$value` and terminate the PHP process immediately by calling the `wp_die()` function. Useful for debugging.

```php
$value = 'A string value';

td($value);
```

## tp($value)

This function prints a `$value`. Useful for debugging.

```php
$value = 42;

tp($value);
```

## themosis_theme_assets()

This function returns the URL path of the `themosis-theme` assets `dist` directory. Useful if you want to point to an asset without using the `Asset` class.

```php
// http://.../wp-content/themes/themosis-theme/dist/images/favicon.ico
$iconUrl = themosis_theme_assets().'/images/favicon.ico';
```

> The previous function `themosis_assets()` is now deprecated.

## themosis_is_post($id)

* **param** _int_
* **return** _boolean_

A function that checks you're on a specified admin page, post or custom post type(edit screen) in order to display a specific content.

```php
$page = get_page_by_title('Home');

if (themosis_is_post($page->ID)) {
    // You're on Home page.
    // Do something for the Home page only.
}
```

## themosis_attachment_id_from_url($url)

* **param** _string_
* **return** _int|boolean_

This function returns an attachment `ID` based on the URL/path given. If no attachment is found, it returns `false`.

```php
$url = 'http://www.domain.com/wp-content/uploads/2014/02/Image150x150.jpg';

// Return the media attachment ID
$id = themosis_attachment_id_from_url($url);
```

## themosis_path($name)

* **param** _string_
* **return** _string_

This function returns the real path of each part of the framework you ask for by providing the `$name` parameter.

Here is the list of available values you can pass:

* **plugin**: Returns the Themosis framework plugin path.
* **sys**: Returns the Themosis framework plugin `src/Themosis` path.
* **storage**: Returns the storage folder path.
* **base**: Returns the theme path.
* **theme**: Returns the theme `resources` path.
* **admin**: Returns the theme `resources/admin` path.

## themosis_set_path(array $paths)

* **param** _array_ A list of paths to register using key/value pairs.

The `themosis_set_path` function allows you to register paths to your project. Those paths are registered globally and are accessible by using the `themosis_path` function.

```php
// Register a new path - A custom folder inside the content directory
$paths['custom'] = WP_CONTENT_DIR.'/custom/';
themosis_set_path($paths);
```

You can then use your path like so:

```php
$path = themosis_path('custom');
```

## themosis_is_subpage($parent)

* **param** _array_ Parent page properties (ID, slug, post name, ...)
* **return** _int|bool_

This function returns whether current post is a child page of the current page. By giving a string in the first index of the `$parent` parameter you can give the slug of the post parent to check where the current page is a child from.

```php
$parent = themosis_is_subpage(['existing-parent-page']) // WP_Post instance of the parent page of the current page
$parent = themosis_is_subpage(['non-existing-parent-page']) // false
```

## themosis_convert_path($path)

* **param** _string_
* **return** _string_

Replaces all '.' characters to the '/' directory seperator 

```php
// assets/images/header
$value = themosis_convert_path('assets.images.header')
```

## themosis_plugin_directory_name()

* **return** _string_

Returns the directory name of the core themosis framework plugin.

## themosis_plugin_url()

* **return** _string_

Retrieves the absolute URL to the core themosis framework plugin.

## themosis_get_the_query()

* **return** _WP_Query_

Returns the current WordPress query inside the `$wp_query` global

## themosis_use_permalink()

* **return** _bool_

Returns whether the WordPress pretty permalink structure is used. 

## themosis_add_filters($tags, $function)

* **param** _array_, _function_
* **return** _bool_

Runs the `add_filter()` function multiple times for every given tag in the `$tags` array parameter using the given function in the `$function` argument.


## themosis_is_template($name = [])

* **param** _array_
* **return** _bool_

Checks whether the themosis template which is currently used is available in the given array of template names.

```php
// Current page template is 'page-template'
themosis_is_template(['test', 'page-template']) // true
```

## e($value)

* **param** _string_ Raw data.
* **return** _string_ Escaped data.

Escapes HTML entities in a string. This function uses the `htmlentities()` PHP core function.

```php
$escaped = e('<p>Raw input</p>');
```

## starts_with($haystack, $needles)

* **param** _string_, _string|array_
* **return** _bool_

Checks whether a given string starts with a given substring.

```php
starts_with('foo bar', 'foo') // true

starts_with('foo bar', ['non-existing-string', 'foo']) // true
```

## array_get($array, $key, $default)

* **param** _array_, _string_, _mixed_
* **return** _mixed_

Get an item of an array using a "." notation

```php
$array = ['items' => [
        'first-item' => 'I am the first item', 
        'second-item' => 'I am the second Item'
    ]
]

$item = array_get($array, 'items.first-item', 'no key found') // 'I am the first item'

$item = array_get($array, 'items.third-item', 'No item found') // 'No item found'
```

## array_set($array, $key, $value)

* **param** _array_ $array
* **param** _string_ $key
* **param** _mixed_ $value
* **return** _array_

Set an array item to a given value using "dot" notation. If no key is given to the method, the entire array will be replaced.

```php
$array = array_set($array, 'items.first', 'blue');
```

## array_except($array, $keys)

* **param** _array_ $array
* **param** _string_ $keys
* **return** _array_

Get all of the given array except for a specified array of items (keys).

```php
$array = array_except(['a' => 1, 'b' => 2, 'c' => 3], ['b']);
```

## array_is_sequential($array)

* **param** _array_ $array
* **return** _bool_

Check if an array is sequential (have keys from 0 to n) or not.

```php
array_is_sequential(['red', 'green', 'blue']); return true
array_is_sequential([3 => 'red', 42 => 'green', 119 => 'blue']); return false
```

## value($value)

* **param** _mixed_ $value
* **return** _mixed_

Return the default value of the given value.

```php
$value = value($var);
```

## with($object)

* **param** _mixed_ $object
* **return** _mixed_

Return the given object. Useful for chaining.

```php
$val = with($instance)->get();
```

## str_contains($haystack, $needles)

* **param** _string_ $haystack
* **param** _string | array_ $needles
* **return** _bool_

Determine if a given string contains a given substring.

```php
str_contains('This is a comment', 'comment'); // return true
str_contains('This is a comment', ['is', 'comment']); // return true
str_contains('This is a comment', 'post'); // return false
```

## app($instance) & container($instance)

* **param** _string_ $instance

Helper function to return any instance registerd into the service container.

```php
$config = container('config'); // return the config instance
$factory = container('view'); // return the view factory instance
$twig = container('twig'); // return the twig environment instance
```

## view($name, $data, $mergeData)

* **param** _string_ $name
* **param** _array_ $data
* **param** _array_ $mergeData
* **return** _string_

Helper function to build views.

```php
$view = view('pages', ['title' => 'Custom page']);
```

## meta($key, $id, $context, $single)
     
* **param** _string_ $key
* **param** _int_ $id
* **param** _string_ $context
* **param** _bool_  $single
* **return** _mixed|string_

Helper function to get any meta data from objects.

```php
$value = meta('_wp_page_template', $id);
$term_order = meta('order', $term_id, 'term');
```
