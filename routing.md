Routing
=======

- [Introduction](#introduction)
- [Basic routing](#basic-routing)
    - [Router methods](#router-methods)
    - [Redirect routes](#redirect-routes)
    - [View routes](#view-routes)
- [Route parameters](#route-parameters)
    - [Optional parameters](#optional-parameters)
    - [Regular expression constraints](#regular-expression-constraints)
    - [The WordPress $post and $query parameters](#the-wordpress-post-and-query-parameters)
- [Named routes](#named-routes)
- [Route groups](#route-groups)
    - [Middleware](#middleware)
    - [Namespace](#namespace)
    - [Route prefixes](#route-prefixes)
    - [Route name prefixes](#route-name-prefixes)
- [Route model binding](#route-model-binding)
    - [Implicit binding](#implicit-binding)
    - [Explicit binding](#explicit-binding)
- [Fallback route](#fallback-route)
- [Managing routes](#managing-routes)
- [WordPress routes](#wordpress-routes)
	- [Conditional tags](#conditional-tags)
	- [Route examples](#route-examples)
	- [Extending WordPress routes](#extending-wordpress-routes)
- [Learn more](#learn-more)

Introduction
------------

In order to handle routes, the Themosis framework is leveraging the `illuminate/routing` package coming from [Laravel](https://laravel.com/docs/5.7/routing).

Basic routing
-------------

All routes for your web interface should be defined into the `routes/web.php` file located at project root. You can define custom and WordPress routes. These routes are assigned to the `web` middleware group, which provides the WordPress conditions bindings and allows you to write routes pointing to WordPress templates.

> We recommend developers to no longer define routes in their theme.

The most basic route accepts a URI or a WordPress condition and a `Closure`:

```php
// Custom route
Route::get('contact', function () {
    return 'Contact Us';
});

// WordPress route using the "single" condition
Route::get('single', function () {
    return 'An article';
});
```

### Router methods

The router provides a method for each HTTP verb:

```php
Route::get($uri, $callback);
Route::post($uri, $callback);
Route::put($uri, $callback);
Route::patch($uri, $callback);
Route::delete($uri, $callback);
Route::options($uri, $callback);
```

Usually WordPress is listening to all HTTP verbs. In order to match WordPress default behavior, you could use the `any()` router method like so:

```php
// Listens to HEAD, GET, POST, PUT, PATCH,... HTTP verbs
Route::any($uri, $callback);
```

If you're using the WordPress Customizer, you might want to use the `any()` method because the Customizer is fetching your pages through a HTTP POST request. So if you're using only the `Route::get()` method for your theme, you get a blank screen when working with the Customizer.

Finally, there is also the `Route::match()` method that lets you define which HTTP verbs you want to listen to:

```php
// Listens to HEAD, GET and POST HTTP verbs.
Route::match(['GET', 'POST'], $uri, function () {
    return 'Hello World!';
});
```

Pass an array of HTTP verbs as the first parameter, followed by an URI (or condition) and a callback.

### Redirect routes

If you are defining a route that redirects to another URI, you may use the `Route::redirect` method. This method provides a convenient shortcut so that you do not have to define a full route or controller for performing a simple redirect:

```php
Route::redirect('/here', '/there', 301);
```

### View routes

If your route only needs to return a view, you may use the `Route::view` method. Like the redirect method, this method provides a simple shortcut so that you do not have to define a full route or controller. The view method accepts a URI as its first argument and a view name as its second argument. In addition, you may provide an array of data to pass to the view as an optional third argument:

```php
Route::view('/contact', 'contact');
Route::view('/contact', 'contact', ['team' => 'IT']);
```

> You cannot pass a WordPress condition to a `Route::redirect` or a `Route::view` method.

Route parameters
----------------

You can now capture segments of an URI thanks to route parameters. For example, you may need to capture the a project's ID from the URL:

```php
Route::get('/projects/{id}', function ($id) {
    return 'Project '.$id;
});
```

You may define as many route parameters as required by your route:

```php
Route::get('/forms/{id}/input/{name}', function ($form_id, $input_name) {
    return "Custom form {$form_id} and its {$input_name} input field instance.";
});
```

Route parameters are always encased within `{}` braces and should consist of alphabetic characters, and may not contain a `-` character. Instead of using the `-` character, use an underscore `_`. Route parameters are injected into route callbacks / controllers based on their order - the names of the callback / controller arguments do not matter.

### Optional parameters

You can indicate a route parameter as optional by appending a `?` to the parameter like so:

```php
Route::get('projects/{id?}', function ($id = 0) {
	return 'One or all projects';
});
```

> Make sure to provide a default value to your route variable.

### Regular expression constraints

You may constrain the format of your route parameters using the `where()` method on a route instance. The `where()` method accepts the name of the parameter and a regular expression defining how the parameter should be constrained:

```php
Route::get('user/{$name}', function ($name) {
    return 'User '.$name;
})->where('name', '[a-zA-Z]+')

Route::get('user/{id}', function ($id) {
    return 'User '.$id;
})->where('id', '[0-9]+');

Route::get('user/{$id}/{$name}', function ($id, $name) {
    //
})->where(['id' => '[0-9]+', 'name' => '[a-zA-Z]+']);
```

#### Global constraints

If you would like a route parameter to always be constrained by a given regular expression, you may use the `pattern()` method. You should define these patterns in the `boot()` method of your `RouteServiceProvider` stored in the `app/Providers` directory:

```php
use Themosis\Support\Facades\Route;

public function boot()
    {
        Route::pattern('name', '[a-zA-Z]+');

        parent::boot();
    }
```

Once the pattern has been defined, it is automatically applied to all routes using that parameter name:

```php
Route::get('contact/{name}', function ($name) {
    //
});
```

> Adding a regular expression constraint directly to your route override the global constraint.

### The WordPress $post and $query parameters

Route parameters are not available to the WordPress routes. But by default, each route callback receives as arguments the WordPress globals `$post` and `$query`.

The first parameter is always the global `$post` and the second parameter is the current WordPress query instance `$query`. Here is an example:

```php
Route::any('page', function ($post, $query) {
    return view('pages', [
        'page' => $post	
    ]);
});
```

Named routes
------------

Named routes allow the convenient generation of URLs or redirects for specific routes. You may specify a name for a route by chaining the `name()` method onto the route definition:

```php
Route::get('user/profile', function () {
    //
})->name('profile');
```

You may also specify route names for controller actions:

```php
Route::get('user/profile', 'UserProfileController@show')->name('profile');
```

> Named routes do not work for WordPress routes.

### Generating URLs To Named Routes

Once you have assigned a name to a given route, you may use the route's name when generating URLs or redirects via the global `route()` function:

```php
// Generating URLs...
$url = route('profile');

// Generating Redirects...
return redirect()->route('profile');
```

If the named route defines parameters, you may pass the parameters as the second argument to the `route()` function. The given parameters will automatically be inserted into the URL in their correct positions:

```php
Route::get('user/{id}/profile', function ($id) {
    //
})->name('profile');

$url = route('profile', ['id' => 1]);
```

### Inspecting The Current Route

If you would like to determine if the current request was routed to a given named route, you may use the `named()` method on a Route instance. For example, you may check the current route name from a route middleware:

```php
/**
 * Handle an incoming request.
 *
 * @param  \Illuminate\Http\Request  $request
 * @param  \Closure  $next
 * @return mixed
 */
public function handle($request, Closure $next)
{
    if ($request->route()->named('profile')) {
        //
    }

    return $next($request);
}
```

Route groups
------------

Route groups allow you to share route attributes, such as middleware or namespaces, across a large number of routes without needing to define those attributes on each individual route. Shared attributes are specified in an array format as the first parameter to the `Route::group` method.

### Middleware

To assign middleware to all routes within a group, you may use the `middleware()` method before defining the group. Middleware are executed in the order they are listed in the array:

```php
Route::middleware(['first', 'second'])->group(function () {
    Route::get('/', function () {
        // Uses first & second Middleware
    });

    Route::get('user/profile', function () {
        // Uses first & second Middleware
    });
});
```

### Namespace

Another common use-case for route groups is assigning the same PHP namespace to a group of controllers using the `namespace()` method:

```php
Route::namespace('Admin')->group(function () {
    // Controllers Within The "App\Http\Controllers\Admin" Namespace
});
```

Remember, by default, the `RouteServiceProvider` includes your route files within a namespace group, allowing you to register controller routes without specifying the full `App\Http\Controllers` namespace prefix. So, you only need to specify the portion of the namespace that comes after the base `App\Http\Controllers` namespace.

### Route prefixes

The `prefix()` method may be used to prefix each route in the group with a given URI. For example, you may want to prefix all route URIs within the group with `admin`:

```php
Route::prefix('admin')->group(function () {
    Route::get('users', function () {
        // Matches The "/admin/users" URL
    });
});
```

### Route name prefixes

The `name()` method may be used to prefix each route name in the group with a given string. For example, you may want to prefix all of the grouped route's names with `admin`. The given string is prefixed to the route name exactly as it is specified, so we will be sure to provide the trailing `.` character in the prefix:

```php
Route::name('admin.')->group(function () {
    Route::get('users', function () {
        // Route assigned name "admin.users"...
    })->name('users');
});
```

Route model binding
-------------------

When injecting a model ID to a route or controller action, you will often query to retrieve the model that corresponds to that ID. The routing package route model binding provides a convenient way to automatically inject the model instances directly into your routes. For example, instead of injecting a user's ID, you can inject the entire User model instance that matches the given ID.

### Implicit binding

The framework automatically resolves Eloquent models defined in routes or controller actions whose type-hinted variable names match a route segment name. For example:

```php
Route::get('api/users/{user}', function (App\User $user) {
    return $user->user_email;
});
```

Since the `$user` variable is type-hinted as the `App\User` Eloquent model and the variable name matches the `{user}` URI segment, the framework will automatically inject the model instance that has an ID matching the corresponding value from the request URI. If a matching model instance is not found in the database, a 404 HTTP response will automatically be generated.

> Make sure to include the `\Illuminate\Routing\Middleware\SubstituteBindings::class` in your HTTP Kernel class.

#### Customizing The Key Name

If you would like model binding to use a database column other than `id` when retrieving a given model class, you may override the `getRouteKeyName()` method on the Eloquent model:

```php
/**
 * Get the route key for the model.
 *
 * @return string
 */
public function getRouteKeyName()
{
    return 'ID';
}
```

### Explicit binding

To register an explicit binding, use the router's `model()` method to specify the class for a given parameter. You should define your explicit model bindings in the `boot()` method of the `RouteServiceProvider` class:

```php
use Themosis\Support\Facades\Route;

public function boot()
{
    parent::boot();

    Route::model('user', App\User::class);
}
```

Next, define a route that contains a `{user}` parameter:

```php
Route::get('profile/{user}', function ($user) {
    //
});
```

Since we have bound all `{user}` parameters to the `App\User` model, a User instance will be injected into the route. So, for example, a request to `profile/1` will inject the User instance from the database which has an ID of `1`.

If a matching model instance is not found in the database, a 404 HTTP response will be automatically generated.

#### Customizing The Resolution Logic

If you wish to use your own resolution logic, you may use the `Route::bind()` method. The Closure you pass to the bind method will receive the value of the URI segment and should return the instance of the class that should be injected into the route:

```php
public function boot()
{
    parent::boot();

    Route::bind('user', function ($value) {
        return App\User::where('name', $value)->first() ?? abort(404);
    });
}
```

Fallback route
--------------

Using the `Route::fallback()` method, you may define a route that will be executed when no other route matches the incoming request. Typically, unhandled requests will automatically render a "404" page via your application's exception handler. However, since you may define the fallback route within your `routes/web.php` file, all middleware in the web middleware group will apply to the route. Of course, you are free to add additional middleware to this route as needed:

```php
Route::fallback(function () {
    //
});
```

The fallback route should always be the last route defined by your application.

> We recommend to use the `fallback()` route instead of the `404` condition provided by WordPress. The `404` route is a catch everything route and may override some of your custom routes.

Managing routes
------------------

The Themosis framework provides multiple locations in order to define application routes. Generally, you'll define routes for the web interface into the `web.php` file stored into the `routes` directory at project's root.

You can also define application routes from a plugin `routes.php` file.

If you define a route from a plugin similar to one of your application routes, the route coming from the plugin will have precedence over the one declared into the root `web.php` file.

Finally we also authorise routes definitions from the theme's `routes.php` file. Any route defined into the theme has precedence over plugin routes and application routes.

> We do not recommend to write routes inside your theme but in a scenario where you need to override a route defined by a plugin, you can use the theme's `routes.php` file to define such route.

WordPress routes
----------------

In addition to custom routes, the Themosis framework provides a way to define WordPress routes. Those "special" routes are based on the [WordPress template conditional tags](https://codex.wordpress.org/Conditional_Tags).

Each WordPress route works by providing a condition instead of a URI. You can find below a list of "condition" terms the framework provides based on WordPress core functions. For example, the condition `home` refers to the WordPress `is_home()` function, the `page` condition to the `is_page()` function and so on...

> Condition terms are defined in the `config/app.php` file. Feel free to add any custom condition to your project.

### Conditional tags

Here is the current list of available route conditional tags in alphabetical order:

```php
'404'
'archive'
'attachment'
'author'
'category' | 'cat'
'date'
'day'
'front' | '/'
'home' | 'blog'
'month'
'page'
'paged'
'postTypeArchive' | 'post-type-archive'
'search'
'single'
'singular'
'sticky'
'tag'
'tax'
'template'
'time'
'year'
```

### Route examples

Before digging into the routing API, remember that when you're developing with the Themosis framework, you're still doing WordPress development. So in WordPress, in order for a route to work, the data has to exists first (usually by posting content from the WordPress administration).

> Please note that routes are read from top to bottom. It may happen that 2 different routes listen to the same URI. For example: a `post-type-archive` and an `archive` routes. The router is always returning the first match. As a best practice, we recommend you to put specific routes (in this case, the `post-type-archive`) on top of your `routes.php` file and generic routes at the bottom or below (in this case the `archive` route).

#### Home route

In the WordPress world, there are 2 routes available for the home page of your website/application:

1. The `home` route
2. The `front` route

##### 1-The home route

This route is mapped to core WordPress function `is_home()`. Historically we could say it handled the home page, now it's the blog main page. The `home` route handles the request to the URI that lists the most recent published posts.

So there are 2 cases:

- If your website is mainly a blog, you can keep the default behaviour and use it as is for the home page and it listens to requests made to this URI `/`
- If your home page is not a main blog page, a custom WordPress page has been defined to display the list of most recent posts, so the `home` route won't handle requests made to `/` URI but perhaps to a `/blog` URI instead. In order for this scenario to work, you must define a custom page from the WordPress administration -> Settings -> Reading panel.

Here's an example of a route listening to the WordPress home page:

```php
Route::any('home', function() {
    return 'Hello World!';
});
```

##### 2-The front route

The `front` route listens to requests made on your application home page only if you define a front page in the WordPress administration -> Settings -> Reading panel. Once a custom page is defined, this route condition listens to the `/` URI:

```php
Route::any('front', function() {	
    return 'Hello World!';
});
```

> Once the front page is defined, the previous `home` condition is no longer used to listen to the `/` URI.

#### Page route

The `page` route condition is listening to requests made to any WordPress page posts. Meaning that in order to work, as previously explained, some data has to be registered first from the WordPress administration.

The `page` condition is mapped to the core function `is_page()`. So by default, the `page` route without parameters is triggered for all existing WordPress pages URI. Here is an example:

```php
Route::any('page', function() {
    return 'Hello World!';
});
```

The above code may match requests done to `/about`, `/contact`, `/some-page`, `/parent/child-page`, ... URIs.

> The `page` route without parameters is a generic route and it is best to register it at the bottom of your `routes.php` file.

##### Route to a specific page

Imagine we have a registered WordPress page with a title of `Contact`. WordPress saves the page post with a post name of `contact` and makes it accessible at the `/contact` URI based on its default permalink structure.

In order to listen to requests made on this contact page, you may write this route:

```php
Route::any('page', ['contact', function() {
    return 'Hello World!';
}]);
```

> We recommend you to write specific page routes above the generic page one.

As it follows the `is_page()` function, you can pass a string (page title, post name or slug), a page ID or an array as the first parameter of the callback array.

##### Route to multiple specific pages

If you want to return same content to multiple specific pages, simply pass an array of pages to the first parameter of the callback array like so:

```php
Route::any('page', [['about', 'contact', 24, 'Our Team'], function() {
    return 'Hello World!';
}]);
```

#### Post route

##### Single post route

In order to listen to requests against a single WordPress post, use the `single` route condition like so:

```php
Route::any('single', function () {
    return 'Hello World!';
});
```

The above sample will work against all single post. If you want to return a different content for a specific post, you can add arguments to the first parameter of the callback array like so:

```php
Route::any('single', ['welcome-post', function () {
    return 'Hello World!';
}]);
```

The above route is only listening to request made to the single post with a slug of `welcome-post`. You can also pass a post ID, title or an array.

##### Archive post route

In order to listen to requests against post archives (paginated), use the `archive` condition like so:

```php
Route::any('archive', function () {
    return 'Paginated posts.';
});
```

> The `archive` route is generic and might handle the display of custom post type archives as well if none defined and post type `has_archive` property set to `true`.

#### Taxonomy route

##### Category route

Core `category` taxonomy routes can be listened to by using the `category` route condition like so:

```php
Route::any('category', function () {
    return 'Category term posts';
});
```

The above code is listening to requests made to all category terms.

If you want to return a different content for a specific category term, simply pass an argument to the first parameter of the callback array like so:

```php
// Listens to URI like /category/tutorials
Route::any('category', ['tutorial', function () {
    return 'Tutorial category posts';
}]);
```

You can pass the same arguments as found in the core function [is_category()](https://developer.wordpress.org/reference/functions/is_category/).

##### Tag route

Core tag taxonomy routes can be listened to by using the `tag` route condition like so:

```php
Route::any('tag', function () {
    return 'Tag term posts';
});
```

The default `tag` route condition is listening to requests made to all tag terms.

If you want to return a different content for a specific category term, simply pass an argument to the first parameter of the callback array like so:

```php
// Listens to URI like /tag/framework
Route::any('tag', ['framework', function () {
    return 'Tag framework posts';
}]);
```

You can pass the same arguments as found in the core function [is_tag()](https://developer.wordpress.org/reference/functions/is_tag/).

##### Custom taxonomy route

If you want to listen to requests done on a custom taxonomy, simply use the `tax` route condition and pass it your custom taxonomy name (slug) like so:

```php
Route::any('tax', ['taxonomy-name', function () {
    return 'Hello World!';
}]);
```

#### Template route

You can route to your WordPress templates on pages and, since WordPress 4.7, on custom post types as well.

Use the `template` route condition and specify your template key name like so:

```php
Route::any('template', ['my-custom-template', function () {
    return 'Hello World!';
}]);
```

The template key string is the slug of your registered template from the `config/templates.php` file stored in your `themosis-theme` or custom plugin if specified.

#### Custom post type route

##### Single custom post route

In order to register a route that listens to your custom post type single posts, use the `singular` route condition and pass it the custom post type name (slug) like so:

```php
Route::any('singular', ['my-custom-post-type', function () {
    return 'Hello World!';
}]);
```

The route is based on the core function [is_singular()](https://developer.wordpress.org/reference/functions/is_singular/). Meaning that one singular route can listen to requests made on multiple custom post types posts.

##### Archive custom post route

If your custom post type is defined as `public => true` and `has_archive => true`, the `post-type-archive` condition will listen to requests made on custom post type archive URI. Simply specify your custom post type name like so:

```php
Route::any('post-type-archive', ['my-custom-post-type', function () {
    return 'Hello World!';
}]);
```

### Extending WordPress routes

You can manage the list of WordPress route conditions available to your application. All conditions are now managed from the `app.php` file stored in the root `config` directory:

```php
'conditions' => [
    'is_404' => '404',
    'is_archive' => 'archive',
    'is_attachment' => 'attachment',
    'is_author' => 'author',
    'is_category' => ['category', 'cat'],
    'is_date' => 'date',
    'is_day' => 'day',
    'is_front_page' => ['/', 'front'],
    'is_home' => ['home', 'blog'],
    ...
]
```

The key is the WordPress conditional tag function signature and the value can be a single string or an array of string conditions. The new API lets you provide multiple names for your condition.

In order to extend available conditions, simply add the signature of your custom conditional tag function and a term. For example, we can now add WooCommerce conditional tags like `is_shop` or `is_product` this way:

```php
'conditions' => [
    ...,
    'is_shop' => 'shop',
    'is_product' => 'product'
]
```

> Note that the WooCommerce shop route is referencing a WordPress page. In order for this route to work, you have to define it before the generic `page` route in your file.

You can now use your custom conditions like so:

```php
Route::any('shop', function () {
    return "List of products";
});
```

Learn more
----------------

### Modifying page title

If you define a custom route in your application and you want to influence the page title you can easily modify it by using the WordPress filter/hook API. The Themosis framework provide a basic wrapper for it.

```php
Route::get('custom/route', function () {
    
    Filter::add('document_title_parts', function ($parts) {
        $parts['title'] = __('Your Custom Page Title', APP_TD);
        return $parts;
    });
    
    return "Content of custom route";
});
```

 

Next
----
Read the [middleware guide]({{url}}/middleware)
