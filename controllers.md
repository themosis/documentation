Controllers
===========

- [Introduction](#introduction)
- [Basic controllers](#basic-controllers)
    - [Defining a controller](#defining-a-controller)
    - [Controllers and namespaces](#controllers-and-namespaces)
    - [Single action controllers](#single-action-controllers)
- [Controller middleware](#controller-middleware)
- [Resource controllers](#resource-controllers)
    - [Partial resource routes](#partial-resource-routes)
    - [Naming resource routes](#naming-resource-routes)
    - [Naming resource route parameters](#naming-resource-route-parameters)
    - [Localizing resource URIs](#localizing-resource-uris)
    - [Supplementing resource controllers](#supplementing-resource-controllers)
- [Dependency injection](#dependency-injection)
- [Route caching](#route-caching)

Introduction
------------

Instead of defining all of your request handling logic in route files, you may wish to organize this behavior using Controller classes. Controllers can group related request handling logic into a class.

Controllers are stored in the `app/Http/Controllers` directory of your application.

Basic controllers
-----------------

### Defining a controller

Here is an example of a basic controller class. By default, controller classes extend the default `Controller` class coming with the framework which provides a few helper methods like the `form()` method which may be used to attach a new form:

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class PageController extends Controller
{
    /**
     * Show the home page.
     */
    public function index()
    {
        return view('home');
    }
}
```

Now based on our code example above, we can use this controller and its method/action to define a route like so:

```php
Route::get('/', 'PageController@index');
```

Now, when a request matches the specified route URI, the `index` method on the `PageController` class will be executed. Of course, the route parameters will also be passed to the method.

> The Themosis framework does not provide all controller base class utility methods you can expect to find from the Laravel framework. Methods currently available are: `validate()` and `middleware()`.

#### Other way to use a controller

Sometimes routes need more parameters. For example when you define a route for a specific page. To use a controller, add the `uses` key to the route callback array and set its value to the controller like so:

```php
Route::get('page', ['about-us', 'uses' => 'About@index']);
```

### Controllers and namespaces

When defining a controller route, it is very important to note that we did not need to specify the full controller namespace. Since the `RouteServiceProvider` loads your route files within a route group that contains the namespace, we only specified the portion of the class name that comes after the `App\Http\Controllers` portion of the namespace.

If you choose to nest your controllers deeper into the `App\Http\Controllers` directory, use the specific class name relative to the `App\Http\Controllers` root namespace. So, if your full controller class is `App\Http\Controllers\Posts\ResourceController`, you should register routes to the controller like so:

```php
Route::get('uri', 'Posts\ResourceController@method');
```

### Single action controllers

If you would like to define a controller that only handles a single action, you may place a single `__invoke` method on the controller:

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class ShowProfile extends Controller
{
    /**
     * Handle the incoming request.
     *
     * @param int $id
     * @param  \Illuminate\Http\Request  $request
     *
     * @return \Illuminate\Http\Response
     */
    public function __invoke($id, Request $request)
    {
        return 'User profile...';
    }
}

```

When registering routes for single action controllers, you do not need to specify a method:

```php
Route::get('user/{id}', 'ShowProfile');
```

You can create an invokable controller by using the `--invokable` option of the `make:controller` console command:

```bash
php console make:controller ShowProfile --invokable
```

Controller middleware
---------------------

[Middleware]({{url}}/middleware) may be assigned to the controller's routes in your route files:

```php
Route::get('profile', 'UserController@show')->middleware('wp.cap');
```

However, it is more convenient to specify middleware within your controller's constructor. Using the `middleware()` method from your controller's constructor, you may easily assign middleware to the controller's action. You may even restrict the middleware to only certain methods on the controller class:

```php
class UserController extends Controller
{
    /**
     * Instantiate a new controller instance.
     *
     * @return void
     */
    public function __construct()
    {
        $this->middleware('wp.can');
        
        $this->middleware('log')->only('index');

        $this->middleware('subscribed')->except('store');
    }
}
```

Controllers also allow you to register middleware using a Closure. This provides a convenient way to define a middleware for a single controller without defining an entire middleware class:

```php
$this->middleware(function ($request, $next) {
    // ...

    return $next($request);
});
```

Resource controllers
--------------------

The framework resource routing assigns the typical "CRUD" routes to a controller with a single line of code. For example, you may wish to create a controller that handles all HTTP requests for "posts" stored by your application. Using the `make:controller` console command, we can quickly create such a controller:

```bash
php console make:controller PostController --resource
```

This command will generate a controller at `app/Http/Controllers/PostController.php`. The controller will contain a method for each of the available resource operations.

Next, you may register a resourceful route to the controller:

```php
Route::resource('posts', 'PostController');
```

This single route declaration creates multiple routes to handle a variety of actions on the resource. The generated controller will already have methods stubbed for each of these actions, including notes informing you of the HTTP verbs and URIs they handle.

You may register many resource controllers at once by passing an array to the resources method:

```php
Route::resources([
    'posts' => 'PostController',
    'tags' => 'TagController'
]);
```

#### Actions handled by resource controller

Verb              | URI                          | Action       | Route Name
--------------------|------------------------------|-----------------|---------------------
GET               | `/posts`                    | index         | posts.index
GET               | `/posts/create`          | create       | posts.create
POST             | `/posts`                    | store         | posts.store
GET               | `/posts/{photo}`        | show         | posts.show
GET               | `/posts/{photo}/edit` | edit            | posts.edit
PUT/PATCH | `/posts/{photo}`        | update       | posts.update
DELETE         | `/posts/{photo}`       | destroy      | posts.destroy

#### Specifying the resource model

If you are using route model binding and would like the resource controller's methods to type-hint a model instance, you may use the` --model` option when generating the controller:

```bash
php console make:controller PostController --resource --model=Post
```

#### Spoofing form methods

Since HTML forms can't make `PUT`, `PATCH`, or `DELETE` requests, you will need to add a hidden `_method` field to spoof these HTTP verbs. The `@method` Blade directive can create this field for you:

```html
<form action="/foo/bar" method="POST">
    @method('PUT')
</form>
```

Partial resource routes
-----------------------

When declaring a resource route, you may specify a subset of actions the controller should handle instead of the full set of default actions:

```php
Route::resource('posts', 'PostController')->only([
    'index', 'show'
]);

Route::resource('posts', 'PostController')->except([
    'create', 'store', 'update', 'destroy'
]);
```

### API resource routes

When declaring resource routes that will be consumed by APIs, you will commonly want to exclude routes that present HTML templates such as `create` and `edit`. For convenience, you may use the `apiResource()` method to automatically exclude these two routes:

```php
Route::apiResource('posts', 'PostController');
```

You may register many API resource controllers at once by passing an array to the `apiResources()` method:

```php
Route::apiResources([
    'posts' => 'PostController',
    'tags' => 'TagController'
]);
```

To quickly generate an API resource controller that does not include the `create` or `edit` methods, use the `--api` option when executing the `make:controller` command:

```bash
php console make:controller Api/PostController --api
```

### Naming resource routes

By default, all resource controller actions have a route name; however, you can override these names by passing a `names` array with your options to the `names()` method:

```php
Route::resource('posts', 'PostController')->names([
    'create' => 'posts.new'
]);
```

### Naming resource route parameters

By default, `Route::resource` will create the route parameters for your resource routes based on the "singularized" version of the resource name. You can easily override this on a per resource basis by using the `parameters()` method. The array passed into the `parameters()` method should be an associative array of resource names and parameter names:

```php
Route::resource('posts', 'PostController')->parameters([
    'posts' => 'article'
]);
```

The example above generates the following URIs for the resource's `show` route:

```bash
/posts/{article}
```

### Localizing resource URIs

By default, `Route::resource()` will create resource URIs using English verbs. If you need to localize the `create` and `edit` action verbs, you may use the `Route::resourceVerbs()` method. This may be done in the `boot()` method of your `AppServiceProvider`:

```php
use Themosis\Support\Facades\Route;

/**
 * Bootstrap any application services.
 *
 * @return void
 */
public function boot()
{
    Route::resourceVerbs([
        'create' => 'creer',
        'edit' => 'editer',
    ]);
}
```

Once the verbs have been customized, a resource route registration such as `Route::resource('posts', 'PostController')` will produce the following URIs:

```bash
/posts/creer

/posts/{post}/editer
```

### Supplementing resource controllers

If you need to add additional routes to a resource controller beyond the default set of resource routes, you should define those routes before your call to `Route::resource`; otherwise, the routes defined by the `resource()` method may unintentionally take precedence over your supplemental routes:

```php
Route::get('posts/popular', 'PostController@method');

Route::resource('posts', 'PostController');
```

Dependency injection
--------------------

The Themosis framework now implements the Illuminate\Container in order to resolve controllers. As a result, you can now type-hint any dependencies your controller may need in its constructor or public methods.

### Constructor injection

Here is an example of a dependency injected in a controller constructor:

```php
<?php

namespace Theme\Controllers;

use Theme\Models;
use Themosis\Route\BaseController;

class Home extends BaseController
{
    /**
     * A books model instance.
     */
    protected $books;

    /*
     * Auto-instantiate a Theme\Models\Books class.
     */
    public function __construct(Books $books)
    {
        $this->books = $books;
    }
}
```

### Method injection

The same principle can be used to controller methods. You can type-hint your dependency just like in the constructor. Here is an example:

```php
<?php

namespace Theme\Controllers;

use Theme\Models;
use Themosis\Route\BaseController;

class Home extends BaseController
{
    /*
     * Auto-instantiate a Theme\Models\Books class.
     */
    public function show(Books $books)
    {
        $books = $books->query()->get();
    }
}
```

If your controller method is also expecting values from route parameters, simply append those parameters after your dependencies. Two possible scenarios for this case:

1. Using a WordPress route
2. Using a custom route

#### Using a WordPress route

Route parameters are not available to WordPress routes. But each time you use a WordPress route, we give you the globals `$post` and `$query` as parameters. In order to access them, simply define them after your method dependencies like so:

```php
public function show(Books $books, $post, $query)
{
    return view('books');
}
```

#### Using a custom route

If you have for example a route defined like so:

```php
Route::get('projects/{id}', 'Projects@show');
```

You can access your route parameter value this way:

```php
public function show(Projects $projects, $id)
{
    // Write some logic...
}
```

Next
----
Read the [models guide]({{url}}/models)