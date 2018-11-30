Middleware
==========

- [Introduction](#introduction)
- [Defining middleware](#defining-middleware)
- [Registering middleware](#registering-middleware)
    - [Global middleware](#global-middleware)
    - [Assigning middleware to routes](#assigning-middleware-to-routes)
    - [Middleware groups](#middleware-groups)
    - [Sorting middleware](#sorting-middleware)
- [Middleware parameters](#middleware-parameters)
- [Terminable middleware](#terminable-middleware)

Introduction
------------

The Themosis framework is now fully compatible with the middleware mechanism provided by the `illuminate/routing` package.

Middleware provide a convenient mechanism for filtering HTTP requests entering your application. For example, the Themosis framework includes a middleware that verifies if the current user has the correct capability to visit a URL. If the current user has not the specified capability defined by the middleware, the user is redirected to the home page. Else if the user has the capability, the middleware allow the request to proceed further into the application.

> Middleware provided by Illuminate packages are not all activated/defined in the HTTP kernel by default. Only the `SubstituteBindings` middleware class is active.

Defining middleware
-------------------

In order to create a new middleware, use the `make:middleware` console command:

```bash
php console make:middleware CheckUserRole
```

This command will create a new `CheckUserRole` class within the `app/Http/Middleware` directory:

```php
<?php

namespace App\Http\Middleware;

use Closure;

class CheckUserRole
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        if ($request->role !== 'editor') {
            return redirect('home');
        }

        return $next($request);
    }
}
```

In the example above, if the given `role` is not an `editor`, the middleware returns a HTTP redirect to the client. Otherwise, the request is passed further into the application. In order to pass the request deeper into the application, call the `$next` callback with the `$request`.

It's best to envision middleware as a series of "layers" HTTP requests must pass through before they hit your application. Each layer can examine the request and even reject it entirely.

> All middleware are resolved via the service container, so you may type-hint any dependencies you need within a middleware's constructor.

Registering middleware
----------------------

### Global middleware

If you want a middleware to run during every HTTP request to your application, list the middleware class in the `$middleware` property of your `app/Http/Kernel.php` class.

### Assigning middleware to routes

If you would like to assign middleware to specific routes, you should first assign the middleware a key in your `app/Http/Kernel.php` file. By default, the `$routeMiddleware` property of this class contains entries for the middleware included with the framework. To add your own, append it to this list and assign it a key of your choosing:

```php
protected $routeMiddleware = [
    'wp.bindings' => \Themosis\Route\Middleware\WordPressBindings::class,
    'wp.can' => \Themosis\Route\Middleware\WordPressAuthorize::class,
    'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class
];
```

Once the middleware has been defined in the HTTP kernel, you may use the middleware method to assign middleware to a route:

```php
Route::get('shop/account', function () {
    //
})->middleware('wp.can');
```

You may also assign multiple middleware to the route:

```php
Route::get('/', function () {
    //
})->middleware('first', 'second');
```

When assigning middleware, you may also pass the fully qualified class name:

```php
use App\Http\Middleware\CheckUserRole;

Route::get('shop/account', function () {
    //
})->middleware(CheckUserRole::class);
```

### Middleware groups

Sometimes you may want to group several middleware under a single key to make them easier to assign to routes. You may do this using the `$middlewareGroups` property of your HTTP kernel.

Out of the box, the Themosis framework comes with `web` and `api` middleware groups that contain common middleware you may want to apply to your web UI and API routes:

```php
protected $middlewareGroups = [
     'web' => [
         'wp.bindings',
         'bindings'
     ],
     'api' => [
         'wp.can:edit_posts',
         'bindings'
     ]
];
```

Middleware groups may be assigned to routes and controller actions using the same syntax as individual middleware. Again, middleware groups make it more convenient to assign many middleware to a route at once:

```php
Route::get('/', function () {
    //
})->middleware('web');

Route::group(['middleware' => ['web']], function () {
    //
});
```

> The `web` middleware group is automatically applied to your `routes/web.php` file by the `RouteServiceProvider`.

### Sorting middleware

Rarely, you may need your middleware to execute in a specific order but not have control over their order when they are assigned to the route. In this case, you may specify your middleware priority using the `$middlewarePriority` property of your `app/Http/Kernel.php` file:

```php
/**
 * The priority-sorted list of middleware.
 *
 * This forces non-global middleware to always be in the given order.
 *
 * @var array
 */
protected $middlewarePriority = [
    'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,
    'wp.bindings' => \Themosis\Route\Middleware\WordPressBindings::class
];
```

Middleware parameters
---------------------

Middleware can also receive additional parameters. For example, your application needs to verify that the current user has a given "capability" before performing a given action, you could create a `CheckCapability` middleware that receives a capability name as an additional argument.

Additional middleware parameters will be passed to the middleware after the `$next` argument:

```php
<?php

namespace App\Http\Middleware;

use Closure;

class CheckCapability
{
    /**
     * Handle the incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @param  string  $role
     * @return mixed
     */
    public function handle($request, Closure $next, $cap)
    {
        if (! current_user_can($cap)) {
            // Redirect...
        }

        return $next($request);
    }
}
```

Middleware parameters may be specified when defining the route by separating the middleware name and parameters with a `:`. Multiple parameters should be delimited by commas:

```php
Route::put('post/{id}', function ($id) {
    //
})->middleware('cap:edit_posts');
```

Terminable middleware
---------------------

Sometimes a middleware may need to do some work after the HTTP response has been prepared. If you define a `terminate` method on your middleware, it will automatically be called after the response is ready to be sent to the browser.

```php
<?php

use Closure;

class Download
{
    public function handle($request, Closure $next)
    {
        return $next($request);
    }

    public function terminate($request, $response)
    {
        // Add necessary response headers...
    }
}
```

The `terminate` method should receive both the request and the response. Once you have defined a terminable middleware, you should add it to the list of route or global middleware in the `app/Http/Kernel.php` file.

When calling the `terminate` method on your middleware, the framework will resolve a fresh instance of the middleware from the [service container](https://laravel.com/docs/5.7/container). If you would like to use the same middleware instance when the `handle` and `terminate` methods are called, register the middleware with the container using the container's `singleton` method.

Next
----
Read the [controllers guide]({{url}}/controllers)
