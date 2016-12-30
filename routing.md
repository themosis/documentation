Routing
=======

- [Introduction](#introduction)
- [Router methods](#router-methods)
- [Managing routes](#managing-routes)
- [WordPress routes](#wordpress-routes)
	- [Conditional tags](#conditional-tags)
	- [Routing examples](#routing-examples)
	- [Extending WordPress routes](#extending-wordpress-routes)
- [Custom routes](#custom-routes)
- [Route parameters](#route-parameters)
    - [Optional parameters](#optional-parameters)
    - [The WordPress $post and $query globals](#the-wordpress-post-and-query-globals)

Introduction
------------

Most of the routes of your website/application will be defined into the `resources/routes.php` file of either your `themosis-theme` theme or custom plugin.

> For the following documentation, code samples assume we're working with the theme `routes.php` file but all samples work as well inside any plugins `routes.php` file.

The route system allows you to define WordPress routes as well as custom ones. It extends the Illuminate/Router package providing extra features like group, controller namespace, middlewares and more.

Router methods
--------------

The router has a method for each HTTP verb:

- `Route::get($uri, $callback)`
- `Route::post($uri, $callback)`
- `Route::put($uri, $callback)`
- `Route::patch($uri, $callback)`
- `Route::delete($uri, $callback)`
- `Route::options($uri, $callback)`

Usually WordPress is listening to all HTTP verbs. In order to match WordPress default behavior, you could use the `any()` router method like so:

```php
// Listens to HEAD, GET, POST, PUT, PATCH,... HTTP verbs
Route::any($uri, $callback);
```

If you're using the WordPress Customizer, you might want to use the `any()` method because the Customizer is fetching your pages through a HTTP POST request. So if you're using only the `Route::get()` method for your theme, you get a blank screen when working with the Customizer.

Finally, there is also the `Route::match()` method that lets you define which HTTP verbs you want to listen to. We do recommend its use for general WordPress templating against the `any()` method. Here is an example:

```php
// Listens to HEAD, GET and POST HTTP verbs.
Route::match(['GET', 'POST'], $uri, function () {
    return 'Hello World!';
});
```

Pass an array of HTTP verbs as the first parameter, followed by an URI (or condition) and a callback.

Managing routes
---------------

A theme is not the only place where you can define routes. You can easily register routes from a custom plugin as well.

The router will use plugin routes as "fallback". Meaning that you can define some routes in a plugin but if the theme registers the same route, it's the route from the theme that will be used. This allows theme developers to overwrite plugin routes and customize the output.

Here is an example. Imagine we have a custom plugin with a registered route that listens to requests made to a custom post type archive of `books`:

```php
use Themosis\Facades\Route;

// Plugin routes.php file
Route::match(['get', 'post'], 'postTypeArchive', ['books', function () {
    return 'Books from the plugin.';
}]);
```

Now the theme developer wants to customize the output for the books page, he can overwrite the plugin route like so from the theme `routes.php` file:

```php
Route::match(['get', 'post'], 'postTypeArchive', ['books', function () {
    return 'Books from the theme.';
}]);
```

When visiting the books page in the browser, the page is only showing "Books from the theme.".

So keep in mind that a theme `routes.php` file has precedence over any plugin `routes.php` files.

Now let's dive into WordPress available routes.

WordPress routes
----------------

The WordPress routes are based on the [WordPress template conditional tags](https://codex.wordpress.org/Conditional_Tags). It doesn't work by providing a specific URI but a condition string. Below is a list of "conditions" terms the framework provides based on WordPress core functions. For example, the condition `home` refers to the core `is_home()` function, the `page` condition to the `is_page()` function and so on.

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

Before digging into the routing API, remember that when you're developing with the Themosis framework, you're still doing WordPress development. So in WordPress, in order for a route to work, the data has to exists first (usually by posting content from the WordPress administration).

> Please note that routes are read from top to bottom. It may happen that 2 different routes listen to the same URI. For example: a `postTypeArchive` and an `archive` routes. The router is always returning the first match. As a best practice, we recommend you to put specific routes (in this case, the `postTypeArchive`) on top of your `routes.php` file and generic routes at the bottom or below (in this case the `archive` route).

#### Home route

In the WordPress world, there are 2 routes available for the home page of your website/application:

1. The `home` route
2. The `front` route

##### 1-The home route

This route is mapped to core WordPress function `is_home()`. Historically we could say it handled the home page, now it's the blog main page. The `home` route handles the request to the URI that lists the most recent published posts.

So there are 2 cases:

- If your website is mainly a blog, you can keep the default behavior and use it as is for the home page and it listens to requests made to this URI `/`
- If your home page is not a main blog page, a custom WordPress page has been defined to display the list of most recent posts, so the `home` route won't handle requests made to `/` URI but perhaps to a `/blog` URI instead. In order for this scenario to work, you must define a custom page from the WordPress administration -> Settings -> Reading panel.

Here's an example of a route listening to the WordPress Home page:

```php
// This route listens to a '/' or '/custom-page' URI
Route::get('home', function() {
    return 'Hello World!';
});
```

> The previous example listens to `GET` and `HEAD` requests on the WordPress home page. But you can listen to other HTTP verbs as recommended earlier.

Here is an example of a `POST` route:

```php
Route::post('home', function() {
    return 'Hello World!';
});
```

##### 2-The front route

The `front` route listens to requests made on your application home page only if you define a front page in the WordPress administration -> Settings -> Reading panel. Once a custom page is defined, this route condition listens to the `/` URI:

```php
Route::get('front', function() {	
    return 'Hello World!';
});
```

> Once the front page is defined, the previous `home` condition is no longer used to listen to the `/` URI.

#### Page routes

The `page` route condition is listening to requests made to any WordPress page posts. Meaning that in order to work, as previously explained, some data has to be registered first from the WordPress administration.

The `page` condition is mapped to the core function `is_page()`. So by default, the `page` route without parameters is triggered for all existing WordPress pages URI. Here is an example:

```php
Route::get('page', function() {
    return 'Hello World!';
});
```

The above code may match requests done to `/about`, `/contact`, `/some-page`, `/parent/child-page`, ... URIs.

> The `page` route without parameters is a generic route and it is best to register it at the bottom of your `routes.php` file.

##### Route to a specific page

Imagine we have a registered WordPress page with a title of `Contact`. WordPress saves the page post with a post name of `contact` and makes it accessible at the `/contact` URI based on its default permalink structure.

In order to listen to requests made on this contact page, you will write this route:

```php
Route::get('page', ['contact', function() {
    return 'Hello World!';
}]);
```

> We recommend you to write specific page routes above the generic page one.

As it follows the `is_page()` function, you can pass a string (page title, post name or slug), a page ID or an array as the **first** parameter of the callback array.

##### Route to multiple specific pages

If you want to return same content to multiple specific pages, simply pass an array of pages to the first parameter of the callback array like so:

```php
Route::get('page', [['about', 'contact', 24, 'Our Team'], function() {
    return 'Hello World!';
}]);
```

#### Post routes

##### Single post route

In order to listen to requests against a single WordPress post, use the `single` route condition like so:

```php
Route::get('single', function () {
    return 'Hello World!';
});
```

The above sample will work against all single post. If you want to return a different content for a specific post, you can add arguments to the first parameter of the callback array like so:

```php
// Depending of your permalink structure, this route might listens to
// a /2016/09/23/welcome-post URI.
Route::get('single', ['welcome-post', function () {
    return 'Hello World!';
}]);
```

The above route is only listening to request made to the single post with a slug of `welcome-post`. You can also pass a post ID, title or an array.

##### Archive post route

In order to listen to requests against post archives (paginated), use the `archive` condition like so:

```php
Route::get('archive', function () {
    return 'Paginated posts.';
});
```

> The `archive` route is generic and might handle the display of custom post type archives as well if none defined and post type `has_archive` property set to `true`.

#### Taxonomy routes

##### Category route

Core `category` taxonomy routes can be listened to by using the `category` route condition like so:

```php
Route::get('category', function () {
    return 'Category term posts';
});
```

The above code is listening to requests made to all category terms.

If you want to return a different content for a specific category term, simply pass an argument to the first parameter of the callback array like so:

```php
// Listens to URI like /category/tutorials
Route::get('category', ['tutorial', function () {
    return 'Tutorial category posts';
}]);
```

You can pass the same arguments as found in the core function [is_category()](https://developer.wordpress.org/reference/functions/is_category/).

##### Tag route

Core 'tag' taxonomy routes can be listened to by using the `tag` route condition like so:

```php
Route::get('tag', function () {
    return 'Tag term posts';
});
```

The default `tag` route condition is listening to requests made to all tag terms.

If you want to return a different content for a specific category term, simply pass an argument to the first parameter of the callback array like so:

```php
// Listens to URI like /tag/framework
Route::get('tag', ['framework', function () {
    return 'Tag framework posts';
}]);
```

You can pass the same arguments as found in the core function [is_tag()](https://developer.wordpress.org/reference/functions/is_tag/).

##### Custom taxonomy route

If you want to listen to requests done on a custom taxonomy, simply use the `tax` route condition and pass it your custom taxonomy name (slug) like so:

```php
Route::get('tax', ['taxonomy-name', function () {
    return 'Hello World!';
}]);
```

> A fix is coming in order to specify a term parameter as well. Stay tuned!

#### Template routes

You can route to your WordPress templates on pages and, since WordPress 4.7, on custom post types as well.

Use the `template` route condition and specify your template key name like so:

```php
Route::get('template', ['my-custom-template', function () {
    return 'Hello World!';
}]);
```

The template key string is the slug of your registered template from the `resources/config/templates.config.php` file stored in your `themosis-theme` or custom plugin if specified.

> Please note that since release 1.3.0 of the Themosis framework, we use WordPress template system. If you migrate from a version anterior to 1.3.0, please update your `_postmeta` database table and replace all records with `meta_key` equivalent to `_themosisPageTemplate` to `_wp_page_template`.

#### Custom post type routes

##### Single custom post route

In order to register a route that listens to your custom post type single posts, use the `singular` route condition and pass it the custom post type name (slug) like so:

```php
Route::get('singular', ['my-custom-post-type', function () {
    return 'Hello World!';
}]);
```

The route is based on the core function [is_singular()](https://developer.wordpress.org/reference/functions/is_singular/). Meaning that one singular route can listen to requests made on multiple custom post types posts.

##### Archive custom post route

If your custom post type is defined as `public => true` and `has_archive => true`, the `postTypeArchive` condition will listen to requests made on custom post type archive URI. Simply specify your custom post type name like so:

```php
Route::get('postTypeArchive', ['my-custom-post-type', function () {
    return 'Hello World!';
}]);
```

### Extending WordPress routes

By default, the router handles most of the WordPress core conditional tags functions.

But for some cases, you'll need more control and be able to listen to additional conditions. Conditions from a plugin like `woocommerce` and their conditional tags like `is_shop`, `is_product` or conditions you'll want to create for a specific route.

In order to define a custom route, you'll need to use the `themosisRouteConditions` filter like so:

```php
// A custom condition function.
function is_test () {
    // Some logic that returns true or false.
    // This sample isn't correct as there is no defined logic
    // here. In order to work, you need to work this out with
    // the WordPress Rewrite API and use the add_rewrite_tag()
    // and add_rewrite_rule() functions in order to create a custom
    // route condition / endpoint.
    return true;
}

// Then add the conditional function to the router.
add_filter('themosisRouteConditions', function ($conds) {
    $conds['test'] = 'is_test';
    return $conds; // Always return an associative array.
});
```

The code snippet above is adding the `test` route condition which will trigger the `is_test()` conditional function.

You can now use this `test` route condition like so:

```php
// In routes.php
Route::get('test', function () {
    return "Hello from the test route.";
});
```
> When using the `themosisRouteConditions` filter, make sure to return an associative array where the key is the route name and its value is the conditional function signature. Read more about the [WordPress Rewrite API.](https://codex.wordpress.org/Rewrite_API)

#### Example of a woocommerce condition

Define a route that checks for a single product page.

```php
// File store in /admin/routing.php
// Keeping all our route definitions in one place.
// Add the product route.
add_filter('themosisRouteConditions', function () {
    $conds['product'] = 'is_product';
    return $conds;
});
```

Then you'll write in your `routes.php` file:

```php
Route::get('product', function () {
    return view('shop.product');
});
```

Custom routes
-------------

Sometimes we do want routing like in any other framework. This could be a single page where we do not want a user to handle its data or custom API endpoints, ...

Since release 1.3.0, the Themosis framework extends the Illuminate/Router package and provides a "traditional" router in addition to the existing WordPress one.

All router methods described previously are avaible (get, post, put, ...). Custom routes work differently than WordPress routes as in this case, you directly specify the URI you want to listen to without the need of generating data before.

Please take note that even if we get more freedom in routing, make sure that your custom routes do not conflict with WordPress routes. It won't break but if WordPress has a route condition available for your URI perhaps it is best to use it. The choice is yours.

Here is an example of custom routes:

```php
Route::get('/sitemap', function () {
    return 'Hello World!';
});

Route::get('/api/v2/projects', function () {
    return 'List of projects';
});

Route::post('/api/v2/projects/', function () {
    return 'Add one project';
});
```

If you decide to use custom routes, make sure to write them at the top of your `routes.php` file so they take precedence over the WordPress ones.

> By looking at the browser "Network", sometimes a custom route response header will return a `404 Not Found`. Each time you define custom routes into your project, make sure to flush the permalink rules either by visiting the WordPress administration -> Settings -> Permalinks page or by using the WP-CLI rewrite flush command from your console or terminal. It only needs to be run once before pushing your project.

Route parameters
----------------

You can now capture segments of an URI thanks to route parameters. For example, by taking our previous custom route to a fake API, we could retrieve an ID to a project route like so:

```php
Route::get('/api/v2/projects/{id}', function ($id) {
    return "Project with ID {$id}";
});
```

You may define as many route parameters as required by your route:

```php
Route::get('/forms/{id}/input/{name}', function ($form_id, $input_name) {
    return "Custom form {$form_id} and its {$input_name} input field instance.";
});
```

> Make sure to sanitize and escape your parameters values before processing any further.

### Optional parameters

You can indicate a route parameter as optional. Append a `?` to the parameter like so:

```php
Route::get('projects/{id?}', function ($id = 0) {
	return 'One or all projects';
});
```

### The WordPress $post and $query globals

Route parameters are not available to the WordPress routes. But by default, each route callback (an anonymous function or a controller method) have as arguments the WordPress globals `$post` and `$query`.

The first parameter is always the global `$post` and the second parameter is the current WordPress query instance `$query`. Here is an example:

```php
Route::any('page', function ($post, $query) {
    return view('pages', [
        'page' => $post	
    ]);
});
```

Next
----
Read the [controllers guide]({{url}}/controllers)