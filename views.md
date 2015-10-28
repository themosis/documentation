Views
=====

- Basic usage
	- Organize your views
	- Passing data to views
	- Sharing data with all views
	- Render your views
- View composers

Basic usage
-----------

Views contain the HTML of your website/application. They provide a convenient way of separating your controller and domain logic from your presentation logic. Views are stored in the `resources/views` directory of your `themosis-theme` theme.

Here is an example of a simple view:

```html
<!-- View stored in resources/views/welcome.php -->
<html>
	<head>
		<title>Welcome WordPress developers</title>
	</head>
	<body>
		<h1>Hello, <?php echo $name; ?></h1>
	</body>
</html>
```

And this view may be returned to the browser like so:

```php
Route::get('home', function()
{
	return View::make('welcome', ['name' => 'Julien']);
});
```

As you can see, the first argument passed to the `View::make()` method is the name of the view file stored in the `resources/views/` directory. The second argument is an array of data available to the view. So inside the view file, you can access the `$name` variable with a value of `Julien` in this example.

### Organize your views

You can organize your views into sub-directories of the `resources/views` directory. Use the "dot" notation to reference your view. For example, if you store a view file at `resources/views/pages/home.php`, you can access it like so:

```php
return View::make('pages.home');
```

> There is no limitation to the number of sub-folders you want to use to organize your views.

### Passing data to views

The `with($key, $value)` method allows you to pass data to your view:

- **$key**: _string|array_ A key name or an array of key => value pair.
- **$value**: _mixed_ The value of your key variable.

```php
// The 'welcome' view has the $name variable with the value of 'Julien'.
$view = View::make('welcome')->with('name', 'Julien');

// Same method but by passing an array inside the with method.
$view = View::make('welcome')->with(array(
	'name'		=> 'Julien',
	'foo'		=> 'Bar'
));
```

Or you can pass a `$data` array as a second parameter in the `make` method like so:

```php
$view = View::make('welcome', array('name' => 'Julien'));
```

#### Share data across all views

The `View::share($key, $value)` method allows you to share data across all used views of your WordPress application.

* **$key**: _string|array_ An array of key => value or simply the key name of your data. The key will be available as a variable inside your views. Take care to not overwrite existing ones.
* **$value**: _mixed_ The value associated to your key.

```php
// Single data shared across views.
// The $user variable with a value of 'FooBar' is available in all views.
View::share('user', 'FooBar');

// Multiple data shared across views.
View::share(array(
	'user'		=> 'FooBar',
	'blog'		=> 'WordPress'
));
```

#### View composers

The `View::composer($views, $callback)` and `View::composers($composers)` methods allows to run a callback or class instance method when a specific view is rendered.

**View::composer($views, $callback):**
- **$views**: _string|array_ One or more views to listen to.
- **$callback**: _Closure|string The callback or class method to run when the defined views are called.

**View::composers($composers):**
- **$composers**: _array_ A list of composers to register/run.

Example of one view composer run when the home page view is called:

```php
// Look at the home page view used on home page URL
Route::get('home', function()
{
	// The view name is 'pages.home'.
	return View::make('pages.home');
});

// Now the following code could be written inside the admin/ folder
// or inside a controller class.
View::composer('pages.home', function($view)
{
	// Pass data to the view.
	$view->with('foo', 'bar');
});
```

You can register one callback to run on multiple views like so:

```php
View::composer(array('pages.home', 'pages.contact'), function($view)
{
	// Pass the same data to the pages.home and pages.contact views.
	$view->with('foo', 'bar');
});
```

In place of using a closure, you can also tell the method to look after a class method instance like so:

```php
// Register a class and load it using the 'loading.config.php` file.
class MyComposerClass
{
	public function add($view)
	{
		// Pass data to the view.
		$view->with('foo', 'Bar');
	}

	// Default method used for a view composer.
	public function compose($view)
	{
	}
}

// The view composer.
// Use same syntax as for controllers.
View::composer('pages.home', 'MyComposerClass@add');
```

You can omit the method name when using a class instance as your composer. By default, the API is looking after a `compose` then a `create` method if the first one do not exists.

```php
// Omit the method name.
View::composer('pages.home', 'MyComposerClass');
```

You can also define multiple composers using the `View::composers` method like so:

```php
View::composers(array(
	'MyComposerClass'		=> 'pages.home',
	'MyComposerClass@add'	=> 'pages.add',
	'OtherComposerClass'	=> array('pages.home', 'pages.product')
));
```

Next
----
Read the [controllers guide](http://framework.themosis.com/docs/controllers/)
