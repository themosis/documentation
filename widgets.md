Widgets
=======

> The widget documentation is not yet up to date with release 2.0.

- [Introduction](#introduction)
- [Register a widget location](#register-a-widget-location)
- [Create a widget](#create-a-widget)

Introduction
------------

The Themosis framework provides a configuration class to register custom widgets stored into a pre-defined folder. By following a class and file name syntax, you can quickly register custom widgets from your theme or a custom plugin.

You can create as many widgets as you want and save them inside a `resources/widgets` directory like in the `themosis-theme` theme.

Register a widget location
--------------------------

Before adding custom widgets, let's define a folder that automatically registers your widgets. In order to add a location to bootstrap your widgets, you need to grab an instance of the Widgets Loader like so:

```php
$loader = container('loader.widget');
```

### Add a location

In order to register a new location, simply call the `add()` method of the Widget Loader and pass an array of paths to any directories you want to look for widgets:

```php
$loader = container('loader.widget');
$loader->add([
    themosis_path('theme.resources').'widgets/'
]);
$loader->load();
```

> You **must** provide an ending `/` to each defined paths.

The above example is the code used by the `themosis-theme` in order to register its `resources/widgets` folder as a widget loader location.

Create a widget
---------------

Now that you have defined folders where to store your custom widgets, let's create one. Here is an example of a custom widget stored for this demonstration inside the `resources/widgets` directory of the `themosis-theme` theme:

```php
// This file is stored in resources/widgets/MyCustomWidget.php
<?php
class MyCustomWidget_Widget extends WP_Widget
{
    public function __construct()
    {
        $params = [
            'description' => 'The widget description',
            'name'        => 'My Custom Widget'
        ];

        parent::__construct('MyCustomWidget_Widget', '', $params);
	}
}
```

In order to auto-register your widgets, you need to follow a class and file names syntax: the class name must be followed by `_Widget` in order to work and your file name should be the same as the class name without the `_Widget` suffix. It is preferred to use camel case syntax for your class names.

> Widgets classes do not currently support namespace, at least for auto-registration. Make sure to properly prefix your class and file names.