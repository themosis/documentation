Controllers
===========

- [Basic usage](#basic-usage)
- [Namespaces](#namespaces)
- [Autoloading](#autoloading)
- [Dependency injection](#dependency-injection)

Basic usage
-----------

Instead of defining all of your route-level logic in a single `routes.php` file, you may wish to organize this behavior using Controller classes.

Controllers can group related route logic into a class. Controllers are stored in the `resources/controllers` directory of your `themosis-theme` theme or custom plugin.

Here is an example of a basic controller class:

```php
<?php
namespace Theme\Controllers;

use Themosis\Route\BaseController;

class Home extends BaseController
{
    public function index()
    {
        return view('pages.home');
    }
}
```

> All your controller classes must extend the `BaseController` class in order to work. Be careful about the namespace `Themosis\Route\BaseController`.

Now based on our code example above, we can use this controller and its method/action from within our `routes.php` file like so:

```php
Route::get('home', 'Home@index');
```

To link a controller to a route, use the following syntax `'ClassName@method'`. In the example above, the route is linked to the `Theme\Controller\Home` class and calls its `index` method.

#### Other way to use a controller

Sometimes routes need more parameters. For example when you define a route for a specific page. To use a controller, add the `uses` key to the route callback array and set its value to the controller like so:

```php
Route::get('page', ['about-us', 'uses' => 'About@index']);
```

Namespaces
----------

We now force developers to use PHP namespaces for the controllers and classes in general. In both the theme and plugin.

A `themosis-theme` theme has its namespace set to `Theme\\` by default. So every controller added into your theme `resources/controllers` directory must have their namespace set to `Theme\Controllers`.

> Check the [plugin guide]({{url}}/plugin) regarding plugin namespace.

So the following theme route example:

```php
Route::match(['get', 'post'], 'home', 'Home@show');
```

is instantiating the `Theme\Controllers\Home` class saved into the theme `resources/controllers/Home.php` file and is calling its `show()` public method.

Here is a sample of the above `Theme\Controllers\Home` class:

```php
<?php
namespace Theme\Controllers;

use Themosis\Route\BaseController;

class Home extends BaseController
{
    public function show()
    {
        return view('pages.home');
    }
}
```

Autoloading
-----------

By default, theme controller classes are loaded using the PSR-4 standard and have a namespace of `Theme\Controllers`.

In order to modify your theme controller namespace and autoloading, use the `loading.config.php` file stored inside the `resources/config` directory of your `themosis-theme`:

```php
<?php

return [

    /*
    * Edit this file in order to configure your theme's
    * classes autoloading. Classes are loaded using PSR-4.
    *
    * The key is the namespace and key's value contains one or more paths to your classes.
    */
    'Theme\\Controllers\\' => themosis_path('theme.resources').'controllers',
    'Theme\\Models\\' => themosis_path('theme.resources').'models',
    'Theme\\Providers\\' => themosis_path('theme.resources').'providers'

];
```

The function `themosis_path('theme.resources')` returns the theme `resources` folder path. More information about the `themosis_path` function in the [Helpers guide]({{url}}/helpers).

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