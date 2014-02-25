Routing
=======

1. Introduction
2. Conditional tags
3. Routes

1. Introduction
===============

Most of the routes of your website/application will be defined in the `app/routes.php` file of your `themosis-theme` theme.

The route system is an enhanced "if" statement. It is based on the [WordPress template conditional tags](https://codex.wordpress.org/Conditional_Tags).

A basic route is using a conditional tag and a closure callback.

2. Conditional tags
===================

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

* **Route::is()** - Accepts no extra parameters than a conditional tag and a closure callback or a controller (check the [controller guide](https://github.com/themosis/documentation/blob/master/controllers.md)).
* **Route::are()** - Accepts extra parameters for multiple "URIs".
* **Route::only()** - Accepts extra parameters for one "URI".

***

### Basic routing
```php
Route::is('home', function(){
	
	return 'Hello World!';

});
```

By default, all routes handle _any_ request type: GET, POST,...

If you define a front page in the WordPress administration, use the `front` conditional tag.

```php
Route::is('front', function(){
	
	return 'Hello World!';

});
```

#### Route to any page
```php
Route::is('page', function(){

	return 'Hello World!';

});
```

#### One route for multiple specific pages
```php
Route::are('page', array('about', 'contact', 24, 'Our Team'), function(){

	return 'Hello World!';

});
```

**Note**: The array you pass as a second parameter is equivalent to the one you pass when using the WordPress conditional tags.

#### One route for a specific page
```php
Route::only('page', 'Contact us', function(){

	return 'Hello World!';

});
```

#### Route to a single post
```php
Route::only('single', 'welcome-post', function(){

	return 'Hello World!';

});
```

#### Route to a Themosis page template
```php
Route::only('template', 'my-custom-template', function(){

	return 'Hello World!';

});
```

This is specific to page templates. The second parameter is the slug of your registered template from the `app/config/templates.config.php` file stored in your `themosis-theme`.

#### Route to custom post type archive
```php
Route::only('postTypeArchive', 'my-custom-post-type', function(){

	return 'Hello World!';

});
```

#### Route to a single custom post type
```php
Route::only('singular', 'my-custom-post-type', function(){

	return 'Hello World!';

});
```

### Route parameters

#### Listen to GET requests
```php
Route::only('single', 'my-post', function(){

	return 'Hello World!';

}, array('method' => 'get'));
```

#### Listen to POST requests
```php
Route::only('single', 'my-post', function(){

	return 'Hello World!';

}, array('method' => 'post'));
```

#### Force HTTPS request
```php
Route::only('single', 'my-post', function(){

	return 'Hello World!';

}, array('ssl' => true));
```
Next
----
Check the [views guide](https://github.com/themosis/documentation/blob/master/views.md)