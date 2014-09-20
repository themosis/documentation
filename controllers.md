Controllers
===========

Instead of defining all of your route-level logic in a single `routes.php` file, you may wish to organize this behavior using Controller classes.

Controllers can group related route logic into a class. Controllers are typically stored in the `app/controllers` directory of your `themosis-theme` theme.

Here is an example of a basic controller class:

```php
<?php

// This class is stored in app/controllers/HomeController.php

class HomeController extends BaseController
{

	public function index(){
	
		return View::make('pages.home');

	}

}

?>
```

It is recommended to store your controllers inside the `app/controllers` directory of your `themosis-theme` theme.

> All your controller classes must extend the `BaseController` class in order to work.

In order to use your controller class, you must add it to the `controllers.config.php` file located in the `app/config` directory of the `themosis-theme` theme. The config file is used to auto-load the controller classes using a class mapping. Add your controller like so:

```php
// Key is the class name and the value is the path to the class file.
'HomeController'	=> themosis_path('app').'controllers'.DS.'HomeController.php'
```

The function `themosis_path('app')` returns the theme `app` folder path. More information about the `themosis_path` function in the [Helpers guide](https://github.com/themosis/documentation/blob/master/helpers.md).

Now we can set a route with this controller and its method/action like so:

```php
Route::get('home', 'HomeController@index');
```

To link a controller to a route, use this syntax `'ClassName@method'`. In the example above, the route is linked to the `HomeController` class and calls the `index` method of that same class.

#### Other way to use a controller

Sometimes routes need more parameters. For example when you define a route for a specific page. To use a controller, you can add the `uses` key to the route parameters and set its value to the controller like so:

```php
Route::get('page', array('about-us', 'uses' => 'AboutController@index'));
```

### Perform actions at controller instantiation

You have the possibility to define a constructor method in your controller class. Using the constructor, you'll be able to perform common actions before the route calls the controller method responsible to render the view.

Here is an example:
```php
<?php

class HomeController extends BaseController
{

	private $property;

	public function __construct(){

		// Do something
		$this->property = 'A value';

	}

	public function index(){
	
		return View::make('pages.home', array('data' => $this->property));

	}

}

?>
```

Next
----
Read the [models guide](http://framework.themosis.com/docs/models/)


