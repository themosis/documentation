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

### Basic route
```php
Route::is('home', function(){
	
	return 'Hello World!';

});
```

By default, all routes handle _any_ request type: GET, POST,...

### Route to any page
```php
Route::is('page', function(){

	return 'Hello World!';

});
```

### One route for mutliple pages
```php
Route::are('page', array('about', 'contact', 24, 'Our Team'), function(){

	return 'Hello World!;

});
```

**Note**: The array you pass as a second parameter is equivalent to the one you pass when using the WordPress conditional tags.

### Route to a single post
```php
Route::only('single', 'welcome-post', function(){

	return 'Hello World!;

});
```

### Route to a Themosis page template
```php

```
