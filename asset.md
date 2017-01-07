Asset
=====

- [Introduction](#introduction)
- [Load assets](#load-assets)
	- [Load asset in the WordPress admin](#load-asset-in-the-wordpress-admin)
	- [Load asset in login screen](#load-asset-in-login-screen)
	- [Load asset in customizer](#load-asset-in-customizer)
	- [Load external assets](#load-external-assets)
	- [Load an asset on a specific request](#load-an-asset-on-a-specific-request)
- [Localize](#localize)
- [Custom attributes](#custom-attributes)
- [Inline code](#inline-code)

Introduction
------------

The `Asset` class is responsible to easily load your stylesheet and/or javascript files.

In order to add/register/enqueue assets, use the `add` method. The method handles both stylesheet and javascript files and the parameters are the same as in the WordPress functions:

* [wp\_enqueue\_script](https://developer.wordpress.org/reference/functions/wp_enqueue_script/)
* [wp\_enqueue\_style](https://developer.wordpress.org/reference/functions/wp_enqueue_style/)

Here are the method details:

```php
Asset::add($handle, $path, $deps = [], $version = '1.0', $mixed = null, $type = '');
```

* `$handle`: _string_. Your custom asset handle name.
* `$path`: _string_. The relative path of your asset from a `dist` directory. You can also provide an external URL.
* `$deps`: _array or boolean_. An array of asset dependencies. _false_ if nothing.
* `$version`: _string or boolean_. A string specifying the asset version number or _false_ for no versioning.
* `$mixed`: _string or boolean_. For stylesheets a _string_ that specify the media type. For javascript files, a _boolean_ that indicate if the asset is loaded before the closing `</head>` tag or in the footer (the theme must insert the `wp_head()` and `wp_footer()` functions).
* `$type`: _string_ Use this parameter if your external asset do not have a file extension. Use `script` or `js` to define a JS file and `style` or `css` to define a CSS file.

Load assets
-----------

By default the `add` method loads the assets in the **front-end**.

```php
// Load a CSS file stored in dist/css/screen.css
Asset::add('my-handle', 'css/screen.css', false, '1.0', 'all');

// Load a JS file stored in dist/js/main.js
Asset::add('my-other-handle', 'js/main.js', ['jquery'], '1.0', true);
```

This example loads, in the front-end of your website/application, a `screen.css` file in the head for `all` media type and a `main.js` file in the footer.

### Load asset in the WordPress admin

In order to change the location for your asset, simply use the `to()` method on your asset:

```php
// This js file is loaded in WordPress admin only
// and is stored in dist/js/custom.js
Asset::add('my-handle', 'js/custom.js', ['backbone'], '1.0', true)->to('admin');
```

Chain the `to()` method after you added your asset.

### Load asset in login screen

```php
Asset::add('my-handle', 'js/custom.js', ['jquery'], '1.0', false)->to('login');
```

> Note: You can only load JS files to the login screen using the Asset API. CSS files are not supported by WordPress.

### Load asset in customizer

```php
Asset::add('my-handle', 'js/custom.js', ['backbone'], '2.0', true)->to('customizer');
```

The `to()` method only accepts two values:

* **admin** : Loads the asset in the WordPress admin area.
* **login** : Loads the asset in the WordPress login area.
* **customizer** : Loads the asset in the WordPress Customizer area.

### Load external assets

The Asset class allows you to load external assets from CDN and others locations,... Simply specify the absolute URL of your asset as a second argument to the `add()` method:

```php
Asset::add('gg-jquery', '//ajax.googleapis.com/ajax/libs/jquery/2.1.1/jquery.min.js', false, '2.1.1', true);
```
The class is looking for the asset extension in order to define if it is a stylesheet or a javascript file. Some externals assets do not show the file extension. In those cases, simply add the `$type` argument. Use `script` or `js` to specify that you load a JS file and use `style` or `css` for a CSS file.

```php
Asset::add('gg-map', 'https://maps.googleapis.com/maps/api/js?key=mysecretkey', false, '3.22', true, 'script');
```

This will load the Google Maps API JS file inside the footer of your theme.

> In case if the Asset class can't register your asset, the class returns a `WP_Error` object. Other alternatives to load those assets is to add them in your header file or use the `wp_head` or `wp_footer` action hooks.

### Load an asset on a specific request

#### By using the routes.php

Inside your Route callback function, you can call the `Asset::add()` method in order to load the asset for the specific URI/page request.

Simply add your code before returning a view:

```php
Route::is('home', function () {
    Asset::add('my-handle', 'js/file.js', ['jquery'], '1.0', true);
    return view('home');
});
```

#### By using a controller

First specify a controller inside your routes.php file.

```php
Route::is('home', 'Pages@index');
```

Then use the constructor or method of your controller to load your asset for this specific URI/page request.

```php
namespace Theme\Controllers;

use Themosis\Facades\Asset;
use Themosis\Route\BaseController;

class Pages
{
    public function __construct()
    {		
        // This asset is only available to the home page
        Asset::add('my-handle', 'css/tool.css', ['main-style'], '1.3', 'screen');
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
View::composer('home', function () {
    Asset::add('my-handle', 'js/myscript.js', false, '1.0.0', true);
});
```

Check the [views guide]({{url}}/views) for more information regarding View composers.

Localize
--------

WordPress allows you to output as a JSON object any data for use in your JS files using the [wp_localize_script()](https://developer.wordpress.org/reference/functions/wp_localize_script/) function.

The asset API directly implements a `localize()` method with the same benefits plus saving you some character input. Once defined, WordPress will output your data right before your script tag so it's available for script use.

Here is an example of the `localize` method:

```php
// Register script asset.
$asset = Asset::add('js-handle', 'js/myscript.js', ['backbone'], '1.0.0', true);

// Localize data for script use.
$asset->localize('variableName', ['book' => 'A book title']);
```

The above code will output this:

```html
<script type="text/javascript">
/* <![CDATA[ */
    var variableName = {
        "book": "A book title"
    };
/* ]]> */
</script>
```

Simply provide a JS variable name and your data to the `localize` method. You can pass any data you want as a second parameter: _string_, _boolean_, _object_, _array_.

Then inside your `js/myscript.js` file, you can access the data like so:

```js
// Retrieve book title.
var title = variableName.book;
```

Custom attributes
-----------------

Since release 1.3.0, you can also define custom HTML attributes to your assets. For example, when using external scripts, sometimes we do want to add a `defer` attribute for better performance.

Use the `addAttributes()` method like so:

```php
$asset = Asset::add('gg-map', 'https://maps.googleapis.com/maps/api/js?key=mysecretkey', false, '3.22', false, 'script');
$asset->addAttributes(['defer', 'data-city' => 'Brussels']);
```

The above code will add the `defer` and `data-city="Brussels"` attributes to your loaded asset.

Inline code
-----------

You can add inline code in order to accompany your loaded assets, both JS and CSS assets.

Use the `inline()` method in order to add code at asset output like so:

```php
// Inline CSS
$css = Asset::add('my-theme', 'css/screen.min.css', false, '1.3.0', 'all');
$css->inline('.panel-main { border: 1px solid blue; }');

// Inline JS
$js = Asset::add('typekit', 'https://use.typekit.net/fdsjhizo.js', false, false);
$js->inline('try{Typekit.load({ async: true });}catch(e){}');
```

By default inline code is always added after loaded asset. For JS assets, you can specify a `before` position in order to load your inline script before your asset:

```php
$js->inline('var app = app || {};', 'before');
```

In above example, the inline script is rendered before the JS asset. The position parameters can accept two values:

 - _after_ (default)
 - _before_