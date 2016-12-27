Routing
=======

- [Introduction](#introduction)
- [WordPress routes](#wordpress-routes)
	- [Conditional tags](#conditional-tags)
	- [Routing examples](#routing-examples)
- [Custom routes](#custom-routes)
- [The $post & $query globals](#the-post-query-globals)

Introduction
------------

Most of the routes of your website/application will be defined into the `resources/routes.php` file of either your `themosis-theme` theme or custom plugin.

> For the following documentation, code samples assume we're working with the theme `routes.php` file but all samples work as well inside any plugins `routes.php` file.

The route system allows you to define WordPress routes as well as custom ones. The Route class extends the Illuminate/Router package providing extra features like group, namespacing, middlewares and more.

The WordPress routes are based on the [WordPress template conditional tags](https://codex.wordpress.org/Conditional_Tags).

A basic WordPress route is using a conditional tag followed by a closure. A custom route is using an URI and a closure.

WordPress routes
----------------

### Conditional tags

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
* template
* time
* year

### Routing examples

Remember that when you're developing with the Themosis framework, you're still doing WordPress development. In WordPress, in order for a route to work, the data has to exists first (usually by posting content from the WordPress administration).

WordPress routes are mapped to WordPress core conditional functions like `is_home()`, `is_page()`, ... so the following routes samples may not have sense directly if you're coming from another traditional framework but stay with us as custom routing is also available [(see below)](#custom-routes)

#### Home route

Set a route for the `WordPress Home` page.

```php
Route::get('home', function(){
    return 'Hello World!';
});
```
The code above listens to `GET` and `HEAD` requests on the home page. But you can listen to other HTTP verbs. Currently the route API handles `HEAD`, `GET` and `POST` HTTP verbs.

Here is an example of a `POST` route:

```php
Route::post('home', function()
{
    return 'Hello World!';
});
```

### Listen to multiple verbs

In some scenarios, you may need to listen to several HTTP verbs. You can do so by using the `match` method:

```php
Route::match(['get', 'post'], 'home', function()
{
    return 'Hello World!';
});
```
The above code will only listen on `GET` and `POST` requests to the home page.

You can also use the `any` method which listen to all HTTP verbs.

```php
Route::any('home', function()
{
    return 'Hello World!';
});
```

### WordPress routes

#### Route to front page

If you define a front page in the WordPress administration, use the `front` conditional tag.

```php
Route::get('front', function()
{	
    return 'Hello World!';
});
```

#### Route to any page

```php
Route::get('page', function()
{
    return 'Hello World!';
});
```

#### Route to one specific page

First, create a new page inside the administration of WordPress. For this example, a page with a title of "Contact" and automatically with a slug/post name of `contact` has been created. In order to listen to request on this contact page, you will write this code:

```php
Route::get('page', ['contact', function()
{
    return 'Hello World!';
}]);
```

**Note**: You can pass a string (page title, post name or slug), a page ID or an array as the **first** parameter of the array.

#### One route for multiple specific pages

```php
Route::get('page', [['about', 'contact', 24, 'Our Team'], function()
{
    return 'Hello World!';
}]);
```

**Note**: Just pass an array equivalent to the one you pass when using the WordPress conditional functions.

#### Route to a single post

```php
Route::get('single', ['welcome-post', function()
{
    return 'Hello World!';
}]);
```

#### Route to a Themosis page template

```php
Route::get('template', ['my-custom-template', function()
{
    return 'Hello World!';
}]);
```

**Note**: This is specific to the framework page templates. The string parameter is the slug of your registered template from the `resources/config/templates.config.php` file stored in your `themosis-theme`.

#### Route to custom post type archive

```php
Route::get('postTypeArchive', ['my-custom-post-type', function()
{
    return 'Hello World!';
}]);
```

#### Route to a single custom post type

```php
Route::get('singular', ['my-custom-post-type', function()
{
    return 'Hello World!';
}]);
```

### Listen to HTTPS requests only.

Simply add as a second parameter the `https` value:

```php
Route::get('single', ['my-post', 'https', function()
{
    return 'Hello World!';
}]);
```

This route doesn't redirect the user to a HTTPS page. This route is only triggered if the current request is secure and using the HTTPS protocol.

### Register custom route conditions

By default, the route class handles most of the WordPress core conditional tags functions.

But for some cases, you'll need more control and be able to listen to additional conditions. Conditions from a plugin like `woocommerce` and their conditional tags like `is_shop`, `is_product` or conditions you'll want to create for a specific route.

In order to define a custom route, you'll need to use the `themosisRouteConditions` filter like so:

```php
// A custom condition function.
function is_test()
{
    // Some logic that returns true or false.
    return true;
}

// Then add the conditional function to the Route class.
add_filter('themosisRouteConditions', function($conds)
{
    $conds['test'] = 'is_test';
    return $conds; // Always return an associative array.
});
```

The code snippet above is adding the `test` route which will listen to the `is_test()` conditional function.

You can now use this `test` route like so:

```php
// In routes.php
Route::get('test', function()
{
    return "Hello from the test route.";
});
```
> When using the `themosisRouteConditions` filter, make sure to return an associative array where the key is the route name and its value is the conditional function signature.

#### Example of a woocommerce route

Define a route that checks for a single product page.

```php
// File store in /admin/routing.php
// Keeps all my route definitions in one place.
// Add the product route.
add_filter('themosisRouteConditions', function()
{
    $conds['product'] = 'is_product';
    return $conds;
});
```

Then you'll write in your `routes.php` file:

```php
Route::get('product', function()
{
    return View::make('shop.product');
});
```

The $post & $query globals
--------------------------

By default, anonymous functions and controller methods have as arguments the globals `$post` and `$query`. 

If you need them, you can grab them like so:

```php
Route::any('page', function($post, $query)
{
    return View::make('pages', [
        'page' => $post	
    ]);
});
```

Next
----
Read the [controllers guide]({{url}}/controllers)