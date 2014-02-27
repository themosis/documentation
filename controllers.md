Controllers
===========

Instead of defining all of your route-level logic in a single `routes.php` file, you may wish to organize this behavior using Controller classes.

Controllers can group related route logic into a class. Controllers are typically stored in the `app/controllers` directory of your `themosis-theme` theme.

Here is an example of a basic controller class:

```php
<?php

// This class is stored in app/controllers/home.controller.php

class Home_Controller
{

	public function index(){
	
		return View::make('pages.home');

	}

}

?>
```

All controller files should be stored inside the `app/controllers` directory of your `themosis-theme` theme and the controller file should be saved with the `.controller.php` extension.

By convention, the name of your file before the extension `.controller.php` must be lowercase and be used as a class name with its first letter to uppercase followed by `_Controller`. This name is also the controller's name used by the `Route` class.

So the controller class file name of the previous example is: `home.controller.php` and its class name is `Home_Controller`.

Now we can route to this controller action like so:

```php
Route::is('home', 'home@index');
```

To link a controller to a route, use this syntax `'name@method'`. In the example above, the route is linked to the `Home_Controller` class and calls the `index` method of that same class.

**Note**: At the moment, you can't organize your controller files in sub-folders like in the `app/views` directory.

### Perform actions at controller instantiation

You have the possibity to define a constructor method in your controller class. Using the constructor, you'll be able to perform common actions before the route calls the controller method responsible to render the view.

Here is an example:
```php
<?php

class Home_Controller
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
This article closes the "Getting started" guide.

Discover the other features/API of the framework below:

* [Ajax](https://github.com/themosis/documentation/blob/master/ajax.md)
* [Asset](https://github.com/themosis/documentation/blob/master/asset.md)
* [PostType](https://github.com/themosis/documentation/blob/master/posttype.md)
* [Field](https://github.com/themosis/documentation/blob/master/field.md)
* [Metabox](https://github.com/themosis/documentation/blob/master/metabox.md)
* Taxonomy
* Page
* Helpers


