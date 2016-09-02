Configuration
=============

- [Basic usage](#basic-usage)
	- [Retrieve all properties](#retrieve-all-properties)
	- [Retrieve a specific property](#retrieve-property)
- [Theme configuration files](#theme-configuration)

<a name="basic-usage"></a>

Basic usage
------------

The Config API has received a completely overhauled API. Using the Config API, you can now retrieve at run-time any configuration property.

Currently, only the theme has configuration files.

The theme configuration files are stored in its `resources/config` folder.

<a name="retrieve-all-properties"></a>

### Retrieve all properties

Use the `get()` method to retrieve all properties of a defined configuration file.

Let's grab all properties of the theme's `application.config.php` file:

```php
$all = Config::get('application');
```

The above code is fetching all properties from the `application.config.php` file stored in the `resources/config` folder. Simply provide the file name without the `.config.php` extension as a parameter of the `get()` method.

```php
// Grab all theme templates
$templates = Config::get('templates');
```

<a name="retrieve-property"></a>

### Retrieve one property

Depending of your application, you might need to retrieve only one specific property from a configuration file.

For this, simply use a dot syntax to specify your property. For example, let's retrieve the `namespace` property from the `application.config.php` file:

```php
$namespace = Config::get('application.namespace');
```

> Note: you can't set/modify configuration values at run-time. Configuration values are read only.

<a name="theme-configuration"></a>

Theme configuration files
-------------------------

Here is a complete list of the configuration files stored inside the theme.

* **application**: Main configuration file. You can specify your own class aliases inside this file.

* **constants**: Allow you to define constant variables.

* **images**: Allow you to register image sizes for your media library.

* **loading**: Allow you to specify directories of classes for autoloading. By default, it is loading your theme's controller and model classes.

* **menus**: Allow you to define custom navigation menu locations. More information in the [WordPress codex](http://codex.wordpress.org/Navigation_Menus).

* **sidebars**: Help you register your sidebars for your website/application. Uses the same arguments found in the [WordPress codex: register_sidebar()](http://codex.wordpress.org/Function_Reference/register_sidebar).

* **supports**: Equivalent to the [add\_theme\_support()](http://codex.wordpress.org/Function_Reference/add_theme_support) function.

* **templates**: Handles the custom page template system of the framework. Define your templates inside this file by providing a `key` or `key/value` pairs.

> Details on how to modify and use those configurations are explained inside each file.