Helpers
=======

Helpers are framework utility functions you can use anywhere. They are functions that run on the global scope.

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
