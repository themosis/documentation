Helpers
=======

Helpers are framework utility functions you can use anywhere. They are functions that run on the global scope.

## td($value)

This function prints a `$value` and terminate the PHP process immediately by calling the `die()` function. Useful for debugging.

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

## themosisAssets()

* **@return** _string_

This function returns the URL path of the `themosis-theme` assets directory. Useful if you want to point an asset without using the `Asset` class.

```php
// Returns a string: http://www.domain.com/wp-content/themes/themosis-theme/app/assets/images/favicon.ico
$iconUrl = themosisAssets().'/images/favicon.ico';
```

> This function also returns the right path if you decide to "hide" real WordPress paths. Check your `application.config.php` file.

## themosisIsPost($id)

* **@param** _int_
* **@return** _boolean_

A function that checks you're on a specified admin page, post or custom post type(edit screen) in order to display a certain content.

```php
$page = get_page_by_title('Home');

$isPage = themosisIsPost($page->ID);
```

## themosisAttachmentIdFromUrl($url)

* **@param** _string_
* **@return** _int|boolean_

This function returns an attachment `ID` based on the URL/path given. If no attachment is found, it returns `false`.

```php
$url = 'http://www.domain.com/wp-content/uploads/2014/02/Image150x150.jpg';

// Return the media attachment ID
$id = themosisAttachmentIdFromUrl($url);
```

## themosis_path($name)

* **@param** _string_
* **@return** _string_

This function returns the real path of each part of the framework you ask for by providing the `$name` parameter.

You can choose 3 values to pass:

* `sys`: Return the `themosis-core` root path.
* `datas`: Return the `themosis-datas``root path.
* `app`: Return the `themosis-theme>app` folder path.


