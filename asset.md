Asset
=====

- Introduction
- Load assets
	- Load asset in the WordPress admin
	- Load asset in login screen
	- Load external assets
	- Load an asset on a specific request
- Localize 

Introduction
------------

The `Asset` class is responsible to easily load your stylesheet and/or javascript files.

In order to add/register/enqueue assets, use the `add` method. The method handles both stylesheet and javascript files and the parameters are the same as in the WordPress functions:

* [wp\_enqueue\_script](http://codex.wordpress.org/Function_Reference/wp_enqueue_script)
* [wp\_enqueue\_style](https://codex.wordpress.org/Function_Reference/wp_enqueue_style)

Here are the method details:

```php
Asset::add($handle, $path, $deps = [], $version = '1.0', $mixed = null, $type = '');
```

* `$handle`: _string_. Your custom asset handle name.
* `$path`: _string_. The relative path of your asset from the `resources/assets` directory. You can also provide external URL.
* `$deps`: _array or boolean_. An array of asset dependencies. _false_ if nothing.
* `$version`: _string or boolean_. A string specifying the asset version number.
* `$mixed`: _string or boolean_. For stylesheets a _string_ that specify the media type. For javascript files, a _boolean_ that indicate if the asset is loaded before the closing `</head>` tag or in the footer (the theme must insert the `wp_head()` and `wp_footer()` functions).
* `$type`: _string_ Use this parameter if your external asset do not have a file extension. Use `script` to define a JS file and `style` to define a CSS file.

Load assets
-----------

By default the `add` method loads the assets in the **front-end**.

```php
// Load a CSS file stored in resources/assets/css/screen.css
Asset::add('my-handle', 'css/screen.css', false, '1.0', 'all');

// Load a JS file stored in resources/assets/js/main.js
Asset::add('my-other-handle', 'js/main.js', ['jquery'], '1.0', true);
```

This example loads, in the front-end of your website/application, a `screen.css` file in the head for `all` media type and a `main.js` file in the footer.

### Load asset in the WordPress admin

In order to change the location for your asset, simply use the `to()` method on your asset:

```php
// This js file is loaded in WordPress admin only
// and is stored in resources/assets/js/custom.js
Asset::add('my-handle', 'js/custom.js', ['backbone'], '1.0', true)->to('admin');
```

Chain the `to()` method after you added your asset.

### Load asset in login screen

```php
Asset::add('my-handle', 'js/custom.js', ['jquery'], '1.0', false)->to('login');
```

> Note: You can only load JS files to the login screen using the Asset API. CSS files are not supported.

The `to()` method only accepts two values:

* **admin** : Loads the asset in the WordPress admin area.
* **login** : Loads the asset in the WordPress login area.

### Load external assets

The Asset class allows you to load external assets from CDN and others, ... Simply specify the absolute URL of your asset as a second argument to the `add()` method:

```php
Asset::add('gg-jquery', '//ajax.googleapis.com/ajax/libs/jquery/2.1.1/jquery.min.js', false, '2.1.1', true);
```
The class is looking for the asset extension in order to define if it is a stylesheet or a javascript file. Some externals assets do not show the file extension. In those cases, simply add the `$type` argument. Use `script` to specify that you load a JS file and use `style` for a CSS file.

```php
Asset::add('gg-map', 'https://maps.googleapis.com/maps/api/js?key=mysecretkey', false, '3.22', true, 'script');
```

This will load the Google Maps API JS file in the footer of your theme.

> In case if the Asset class can't register your asset, the class returns a `WP_Error` object. Other alternatives to load those assets is to add them in your header file or use the `wp_head` or `wp_footer` action hooks.

### Load an asset on a specific request

#### By using the routes.php

Inside your Route callback function, you can call the `Asset::add()` method in order to load the asset for the specific URI/page request.

Simply add your code before returning a view:

```php
Route::is('home', function()
{
	Asset::add('my-handle', 'js/file.js', ['jquery'], '1.0', true);
	return View::make('home');
});
```

#### By using a controller

First specify a controller inside your routes.php file.

```php
Route::is('home', 'home@index');
```

Then use the constructor method of your controller to load your asset for this specific URI/page request.

```php
// File stored in resources/controllers/HomeController.php
class HomeController
{
	public function __construct()
    {		
		// This asset is only available to the home page
		Asset::add('my-handle', 'css/tool.css', array('main-style'), '1.0', 'screen');
	}
	
	// Method responsible to render the home view
	public function index()
    {	
		return View::make('home');
	}
}
```

#### By using a view composer

View composer allows you to run code when a specific view is rendered.

```php
View::composer('home', function()
{
    Asset::add('my-handle', 'js/myscript.js', false, '1.0.0', true);
});
```

Check the [views guide](http://framework.themosis.com/docs/views/) for more information regarding View composers.
