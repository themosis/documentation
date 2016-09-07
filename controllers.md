Controllers
===========

- [Basic usage](#basic-usage)
	- [Autoloading classes](#autoloading-classes)
- [Perform actions at controller instantiation](#perform-actions-at-controller-instantiation)
- [The layout property](#the-layout-property)

Basic usage
-----------

Instead of defining all of your route-level logic in a single `routes.php` file, you may wish to organize this behavior using Controller classes.

Controllers can group related route logic into a class. Controllers are stored in the `resources/controllers` directory of your `themosis-theme` theme.

Here is an example of a basic controller class:

```php
<?php
// This class is stored in resources/controllers/HomeController.php
class HomeController extends BaseController
{
    public function index()
    {
        return View::make('pages.home');
    }
}
?>
```

> All your controller classes must extend the `BaseController` class in order to work.

Now, using this controller and its method/action, we can specify the route like so:

```php
Route::get('home', 'HomeController@index');
```

Here is another example with a generic page route:

```php
Route::get('page', 'HomeController@index');
```

To link a controller to a route, use the following syntax `'ClassName@method'`. In the example above, the route is linked to the `HomeController` class and calls the `index` method of that same class.

#### Other way to use a controller

Sometimes routes need more parameters. For example when you define a route for a specific page. To use a controller, add the `uses` key to the route parameters and set its value to the controller like so:

```php
Route::get('page', ['about-us', 'uses' => 'AboutController@index']);
```

### Autoloading classes

By default, controller (and model) classes are loaded using the PSR-4 standard and are setup without any namespaces.

If you want to add directories to autoload custom classes or add namespaces to your existing controllers, use the `loading.config.php` file stored inside the `resources/config` directory of your `themosis-theme`:

```php
<?php

return [
    /*
    * Edit this file in order to configure your theme's
    * classes autoloading. Classes are loaded using PSR-4.
    *
    * The key is the namespace and key's value contains one or more paths to your classes.
    */
    ''  => [themosis_path('theme').'controllers', themosis_path('theme').'models']
];
```

The function `themosis_path('theme')` returns the theme `resources` folder path. More information about the `themosis_path` function in the [Helpers guide](http://framework.themosis.com/docs/helpers/).

Perform actions at controller instantiation
-------------------------------------------

Each time you use a controller within your routes, the controller is automatically instantiated for you before calling the defined method in the route.

This means that you can define the constructor method and perform common actions before the route calls the controller method responsible to render the view.

Here is an example:
```php
<?php

class HomeController extends BaseController
{
    protected $property;

    public function __construct()
    {
        // Do something
        $this->property = 'A value';
    }

    public function index()
    {	
        return View::make('pages.home', ['data' => $this->property]);
    }
}
?>
```

The layout property
-------------------

When you create a controller class, you must extend the `BaseController` class. Thanks to this parent class, your controller contains an instance property called `$layout` which you can override.

The layout property is useful in the case where all your controller methods need to return the same view or if you want to provide a default view which can of course be overriden in your controller method.

Also note that once you defined a layout property, it automatically becomes a view instance inside the controller method.

```php
class PageController extends BaseController
{
    protected $layout = 'pages.common';

    public function all()
    {
        // $layout is a view instance
        // I can pass data to it using the with() method
        $this->layout->with('key', 'hello');
    }
}
```

In the code example above, when a route calls the `PageController@all` method, it will return the `pages.common` view with the added data `$key` with a value of `hello`.

As mentioned earlier, even if you define a `$layout` property, you can still override the view you want to return inside the method like so:

```php
class PageController extends BaseController
{
    protected $layout = 'pages.common';

    public function all()
    {
        return View::make('pages.custom');
    }
}
```

Next
----
Read the [models guide]({{url}}/models)