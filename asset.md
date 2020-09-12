Asset
=====

- [Introduction](#introduction)
- [Load assets](#load-assets)
    - [Load asset in the WordPress admin](#load-asset-in-the-wordpress-admin)
    - [Load asset in login screen](#load-asset-in-login-screen)
    - [Load asset in customizer](#load-asset-in-customizer)
    - [Load asset in multiple locations](#load-asset-in-multiple-locations)
    - [Load external assets](#load-external-assets)
    - [Load an asset on a specific request](#load-an-asset-on-a-specific-request)
- [Localize](#localize)
- [Custom attributes](#custom-attributes)
- [Inline code](#inline-code)
- [Register asset locations](#register-asset-locations)

Introduction
------------

The `Asset` class is responsible to easily load your stylesheet javascript files.

In order to enqueue asset, use the `add()` method. The method handles both stylesheet and javascript files and the parameters are the same as in the WordPress functions:

* [wp\_enqueue\_script](https://developer.wordpress.org/reference/functions/wp_enqueue_script/)
* [wp\_enqueue\_style](https://developer.wordpress.org/reference/functions/wp_enqueue_style/)

Here are the method details:

```php
Asset::add($handle, $path, $dependencies = [], $version = null, $arg = null);
```

* $handle _string_: Your custom asset handle name.
* $path _string_: The relative path of your asset from a `dist` directory. You can also provide an external URL.
* $dependencies _array_: An array of asset dependencies.
* $version _string_: A string specifying the asset version number or _false_ for no versioning.
* $arg _string or boolean_: For stylesheets a _string_ that specify the media type. For javascript files, a _boolean_ that indicate if the asset is loaded before the closing `</head>` tag or in the footer (the theme must insert the `wp_head()` and `wp_footer()` functions).

Load assets
-----------

Calling the `add` method no longer automatically loads the asset. In order to load the asset, call the `to()` method like so:

```php
// Load a CSS file stored in dist/css/screen.css
Asset::add('my-handle', 'css/screen.css', [], '1.0', 'all')->to();

// Load a JS file stored in dist/js/main.js
Asset::add('my-other-handle', 'js/main.js', ['jquery'], '1.0', true)->to();
```

This example loads, in the front-end of your website/application, a `screen.css` file in the head for `all` media type and a `main.js` file in the footer.

By default, calling the `to()` method only enqueue the asset on the front-end.

### Load asset in the WordPress admin

In order to load the asset for the WordPress administration, simply pass the `admin` parameter the `to()` method of your asset:

```php
// This js file is loaded in WordPress admin only
// and is stored in dist/js/custom.js
Asset::add('my-handle', 'js/custom.js', ['backbone'], '1.0', true)->to('admin');
```

### Load asset in login screen

Pass the `login` parameter on the `to()` method:
```php
Asset::add('my-handle', 'js/custom.js', ['jquery'], '1.0', false)->to('login');
```

> Note: you can only load JS files to the login screen using the Asset API. CSS files are not supported by WordPress.

### Load asset in customizer

In order to load an asset to the customizer, pass the `customizer` parameter on the `to()` method:

```php
Asset::add('my-handle', 'js/custom.js', ['backbone'], '2.0', true)->to('customizer');
```

### Load asset in multiple locations

If you need to load an asset on both the front-end and the administration of your application, you can pass an array of locations on the `to()` method like so:

```php
Asset::add('handle', 'js/script.js', [], '1.0', true)->to(['front', 'admin']);
```

The `to()` method accepts the following values:

* **front** : Load the asset on the front-end area.
* **admin** : Load the asset in the WordPress administration area.
* **login** : Load the asset in the WordPress login area.
* **customizer** : Load the asset in the WordPress customizer area.

### Load external assets

The Asset class allows you to load external assets from CDN and others locations,... Simply specify the absolute URL of your asset as a second argument to the `add()` method:

```php
Asset::add('gg-jquery', '//ajax.googleapis.com/ajax/libs/jquery/2.1.1/jquery.min.js', [], '2.1.1', true);
```
The class is looking for the asset extension in order to define if it is a stylesheet or a javascript file. Some externals assets do not show the file extension. In those cases, simply call the `setType()` method:

```php
Asset::add('gg-map', 'https://maps.googleapis.com/maps/api/js?key=mysecretkey', [], '3.22', true)
    ->setType('script');
```

Use `script` to the `setType()` method to specify that you're loading a JavaScript file and use `style` for a CSS file.

### Load an asset on a specific request

#### By using the routes.php

Inside your Route callback function, you can call the `Asset::add()` method in order to load the asset for the specific URI/page request.

Simply add your code before returning a view:

```php
Route::is('home', function () {
    Asset::add('my-handle', 'js/file.js', ['jquery'], '1.0', true)->to();
    return view('home');
});
```

#### By using a controller

First specify a controller inside your routes.php file.

```php
Route::is('home', 'Pages@index');
```

Then use the constructor or method of your controller to load your asset for this specific URI/page request.

If you're using a controller class to handle multiple routes, call the asset from its constructor will "share" the asset between all requests. Use a controller method instead if you want to load the asset for a specific route.

```php
<?php

namespace App\Http\Controllers;

use Themosis\Support\Facades\Asset;

class Pages extends Controller
{
    public function __construct()
    {		
        // Call assets here in order to share them between multiple requests.
    }
	
    // Method responsible to render the home view
    public function index()
    {	
        // This asset is only available to the home page
        Asset::add('my-handle', 'css/tool.css', ['main-style'], '1.3', 'screen')->to();
        
        return view('home');
    }
}
```

#### By using a view composer

View composer allows you to run code when a specific view is rendered. You can also enqueue an asset through the view composer call:

```php
View::composer('home', function () {
    Asset::add('my-handle', 'js/myscript.js', [], '1.0.0', true)->to();
});
```

Check the [views guide]({{url}}/views) for more information regarding View composers.

Localize
--------

WordPress allows you to output as a JSON object any data for use in your JavaScript files using the [wp_localize_script()](https://developer.wordpress.org/reference/functions/wp_localize_script/) function.

The asset API directly implements a `localize()` method with the same benefits plus saving you some character input. Once defined, WordPress will output your data right before your script tag so it's available for script use.

Here is an example of the `localize` method:

```php
// Register script asset.
$asset = Asset::add('js-handle', 'js/myscript.js', ['backbone'], '1.0.0', true)->to();

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

Simply provide a JavaScript variable name and your data to the `localize` method. You can pass any data you want as a second parameter: _string_, _boolean_, _object_, _array_.

Then inside your `js/myscript.js` file, you can access the data like so:

```js
// Retrieve book title.
var title = variableName.book;
```

Custom attributes
-----------------

You can also define custom HTML attributes to your assets. Use the `attributes()` method like so:

```php
$asset = Asset::add('gg-map', 'https://maps.googleapis.com/maps/api/js?key=mysecretkey', [], '3.22', false)
    ->setType('script')
    ->attributes(['defer', 'data-city' => 'Brussels'])
    ->to();
```

The above code will add the `defer` and `data-city="Brussels"` attributes to your loaded asset.

Inline code
-----------

You can add inline code in order to accompany your loaded assets. Use the `inline()` method in order to add code at asset output like so:

```php
// Inline CSS
$css = Asset::add('my-theme', 'css/screen.min.css', [], '1.3.0', 'all')->to();
$css->inline('.panel-main { border: 1px solid blue; }');

// Inline JS
$js = Asset::add('typekit', 'https://use.typekit.net/fdsjhizo.js', [], false)->to();
$js->inline('try{Typekit.load({ async: true });}catch(e){}');
```

By default inline code is always added after loaded asset. For JS assets, you can specify a `before` position in order to load your inline script before your asset:

```php
$js->inline('var app = app || {};', 'before');
```

In above example, the inline script is rendered before the JavaScript asset.

The second argument allows you to define the position of the inline code. You can render your code before or after the asset tag. By default, inline code is rendered "after" the asset. If you want to render the inline code before the asset, simply pass `false` to the position parameter:

```php
$js->inline('var app = app || {}', false);
```

Register asset locations
------------------------

You can define custom assets locations for your application in the root `config/assets.php` configuration file.

In order to register a new asset location, you can pass a key value pair to the configuration `paths` property where the key is the path to your compiled asset and the value its URL:

```php
'paths' => [
    web_path('dist') => rtrim(config('app.url'), '\/').'/dist'
],
```

Additional assests locations may also be defined by plugins and themes using the `$theme->assets()` and `$plugin->assets()` calls.

The Themosis theme boilerplate is configured to load assets from its `dist` directory (configured in `functions.php`). Same is configured for Themosis plugin boilerplate (in `plugin-name.php`).

### Assets load order

Themosis searches for the (non-external) assets files set up by `Asset::add()` in the configured assets locations in the order the locations are added.

In other words Themosis loads the first matching asset file found in this order:
1. Search for the asset file under the locations configured in the root `config/assets.php` file, one-by-one, in the order they are added.
2. Search for the asset file under the locations configured in the active plugins, one-by-one, in the order they are added.
3. Search for the asset file under the locations configured in the active theme, one-by-one, in the order they are added.

If the asset file cannot be found under any of these locations, an `AssetException` is thrown.

Please note, that the particular order in which different plugins are loaded is controlled by WordPress. Must use plugins are loaded before other plugins, but the exact order between the different plugins may vary. Therefore it is not recommended to rely on the load order of plugins when loading assets.

When your application uses multiple Themosis plugins, it is best to use unique asset names to prevent conflicts between them. It is also advisable to set unique name for theme assets, as any matching file in an active plugins assets location can potentially "hijack" the loading of the theme asset files. A unique asset name may as well be achieved by using an unique subfolder together with a generic filename e.g.: `'js/some-unique-string/main.js'` or `'css/some-unique-string/style.js'`
