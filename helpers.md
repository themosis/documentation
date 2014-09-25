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

## themosis_assets()

This function returns the URL path of the `themosis-theme` assets directory. Useful if you want to point to an asset without using the `Asset` class.

```php
// http://.../wp-content/themes/themosis-theme/app/assets/images/favicon.ico
$iconUrl = themosis_assets().'/images/favicon.ico';
```

## themosis_is_post($id)

* **param** _int_
* **return** _boolean_

A function that checks you're on a specified admin page, post or custom post type(edit screen) in order to display a specific content.

```php
$page = get_page_by_title('Home');

if (themosis_is_post($page->ID))
{
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

* **sys**: Returns the Themosis framework plugin `src/Themosis` path.
* **base**: Returns the `themosis-theme` root path.
* **app**: Returns the `themosis-theme/app` folder path.
* **admin**: Returns the `themosis-theme/app/admin` folder path.
* **storage**: Returns the `themosis-theme/app/storage` folder path.


