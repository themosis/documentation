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

By convention, the name of your file before the extension `.controller.php` must be lowercase and be used as a class name with its first letter to uppercase followed by `_Controller`.

So the controller class file name of the previous example is: `home.controller.php` and its class name is `Home_Controller`.