Asset
=====

The `Asset` class is responsible to easily load your stylesheet and/or javascript files.

By default the class loads the assets in the **front-end**.

```php

// Load a CSS file
Asset::add('my-handle', 'css/screen.css', false, '1.0', 'all');

// Load a JS file stored in app/assets/js/main.js
Asset::add('my-other-handle', 'js/main.js', array('jquery'), '1.0', true);

```

This example loads a `screen.css` file in the front-end of your website/application. The file is stored in the `app/assets/css/` directory of your `themosis-theme` theme.

> The `Asset::add()` method handles both stylesheet and javascript files.

The parameters you insert in the `Asset::add()` method are the same from the WordPress methods:

* [wp\_enqueue\_script function](http://codex.wordpress.org/Function_Reference/wp_enqueue_script)
* [wp\_enqueue\_style function](https://codex.wordpress.org/Function_Reference/wp_enqueue_style)

Here is the method details:
```php
Asset::add($handle, $path, $deps, $version, $mixed);
```

* `$handle`: _string_. Your custom asset handle name.
* `$path`: _string_. The relative path of your asset from the `app/assets` directory of your `themosis-theme` theme.
* `$deps`: _array or boolean_. An array of asset dependencies. _false_ if nothing.
* `$version`: _string or boolean_. A string specifying the asset version number.
* `$mixed`: _string or boolean_. For stylesheets a _string_ that specify the media type. For javascript files, a _boolean_ that indicate if the asset is loaded before the closing `</head>` tag or in the footer (the theme must insert the `wp_footer()` function).

### Load an asset to the admin area

You can point your asset to the WordPress admin if you need to.

```php

// This js file is loaded in WordPress admin only
// and is stored in app/assets/js/custom.js
Asset::add('my-handle', 'js/custom.js', array('backbone'), '1.0', true)->to('admin');
```

Chain the `to()` method after you added your asset.

### Load an asset to the login area

```php

Asset::add('my-handle', 'js/custom.js', array('jquery'), '1.0', false)->to('login');

```

The `to()` method only accepts two values:

* **admin** : Loads the asset in the WordPress admin area.
* **login** : Loads the asset in the WordPress login area.

### Load an asset on a specific URI
