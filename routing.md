Routing
=======

1. Introduction
2. Conditional tags
3. Routes
4. The Globals

1. Introduction
---------------

Most of the routes of your website/application will be defined in the `app/routes.php` file of your `themosis-theme` theme.

The route system is an enhanced "if" statement. It is based on the [WordPress template conditional tags](https://codex.wordpress.org/Conditional_Tags).

A basic route is using a conditional tag and a closure callback.

2. Conditional tags
-------------------

Here is the current list of available route conditional tags in alphabetical order (**bold** = extra conditional tags):

* 404
* archive
* attachment
* author
* category
* date
* day
* front
* home
* month
* page
* paged
* postTypeArchive
* search
* single
* singular
* sticky
* **subpage**
* tag
* tax
* **template**
* time
* year

3. Routes
---------

There are 3 methods for the Route class:

* **Route::get()** - Listen to `GET` and `HEAD` HTTP requests.

* **Route::post()** - Listen to `POST` HTTP requests.

* **Route::any()** - Currently listening to `GET`, `HEAD`, `POST` requests.

***

### Basic routing

Set a route for the `WordPress Home` page.

```php
Route::get('home', function(){
	
	return 'Hello World!';

});
```

If you define a front page in the WordPress administration, use the `front` conditional tag.

```php
Route::get('front', function(){
	
	return 'Hello World!';

});
```

#### Route to any page
```php
Route::get('page', function(){

	return 'Hello World!';

});
```

#### Route to one specific page
```php
Route::get('page', array('contact', function(){

	return 'Hello World!';

}));
```

**Note**: Just pass a string, a page ID or an array as the **first** parameter in the array.

#### One route for multiple specific pages
```php
Route::get('page', array(array('about', 'contact', 24, 'Our Team'), function(){

	return 'Hello World!';

}));
```

**Note**: Just pass an array equivalent to the one you pass when using the WordPress conditional tags.

#### Route to a single post
```php
Route::get('single', array('welcome-post', function(){

	return 'Hello World!';

}));
```

#### Route to a Themosis page template
```php
Route::get('template', array('my-custom-template', function(){

	return 'Hello World!';

}));
```

**Note**: This is specific to the framework page templates. The string parameter is the slug of your registered template from the `app/config/templates.config.php` file stored in your `themosis-theme`.

#### Route to custom post type archive
```php
Route::get('postTypeArchive', array('my-custom-post-type', function(){

	return 'Hello World!';

}));
```

#### Route to a single custom post type
```php
Route::get('singular', array('my-custom-post-type', function(){

	return 'Hello World!';

}));
```

#### Listen to POST requests
```php
Route::post('single', array('my-post', function(){

	return 'Hello World!';

}));
```

#### Listen to ANY requests

The route API gives you a method to listen on both `GET` and `POST` request:

```php
Route::any('page', function(){

	return 'Hello World!';

});
```

#### Listen to HTTPS requests only.

Simply add as a second parameter the `https` value:

```php
Route::get('single', array('my-post', 'https', function(){

	return 'Hello World!';

}));
```

This route doesn't redirect the user to a HTTPS page. This route is only triggered if the current request is secure and using the HTTPS protocol.

> If you save asset URL in custom fields and using them by calling the `Meta` class, they will be auto-converted to their `https` equivalent.

4. The Globals
--------------

By default, anonymous functions and controller methods have as arguments the globals `post` and `wp_query`. 

If you need them, you can grab them like so:

```php
Route::any('page', function($post, $query){

    return View::make('pages', array(
	
		'page'		=> $post	

	));

});
```

Next
----
Read the [views guide](https://github.com/themosis/documentation/blob/master/views.md)