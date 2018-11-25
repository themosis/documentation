Configuration
=============

- [Basic usage](#basic-usage)
	- [Retrieve all properties](#retrieve-all-properties)
	- [Retrieve a specific property](#retrieve-a-specific-property)
- [Theme configuration files](#theme-configuration-files)

Basic usage
------------

The Config API has received a completely overhauled API. Using the Config API, you can now retrieve at run-time any configuration property.

Configuration files are stored in `resources/config` folders.

### Retrieve all properties

Use the `get()` method to retrieve all properties of a defined configuration file.

Let's grab all properties of the theme's `theme.config.php` file:

```php
use Themosis\Facades\Config;

$all = Config::get('theme');
```

The above code is fetching all properties from the `theme.config.php` file stored in the `resources/config` folder. Simply provide the file name without the `.config.php` extension as a parameter of the `get()` method.

```php
use Themosis\Facades\Config;

// Grab all theme templates
$templates = Config::get('templates');
```

#### Plugin configuration file names

When creating configuration files for a custom plugin, we recommend you to prefix your configuration file names with your domain tld, domain and plugin name in front of the configuration base name in order to avoid conflicts.

If your plugin creates a `templates.config.php` file, it will break the theme's `templates.config.php` configuration.

Here is a valid `templates` configuration file for a custom plugin: `com_domain_shop_templates.config.php`

And in order to retrieve it from your plugin:

```php
$templates = Config::get('com_domain_shop_templates');
```

### Retrieve a specific property

Depending of your application, you might need to retrieve only one specific property from a configuration file.

For this, simply use a dot syntax to specify your property. For example, let's retrieve the `namespace` property from the `theme.config.php` file:

```php
$namespace = Config::get('theme.namespace');
```

> Note: you can't set/modify configuration values at run-time. Configuration values are read only.

Theme configuration files
-------------------------

Here is a complete list of the configuration files stored inside the theme.

* **constants**: Allow you to define constant variables.
* **images**: Allow you to register image sizes for your media library.
* **loading**: Allow you to specify directories of classes for autoloading. By default, it is loading your theme's controller and model classes.
* **menus**: Allow you to define custom navigation menu locations. More information in the [WordPress codex](http://codex.wordpress.org/Navigation_Menus).
* **providers**: Allow you to define the list of theme's service providers.
* **sidebars**: Help you register your sidebars for your website/application. Uses the same arguments found in the [WordPress codex: register_sidebar()](https://developer.wordpress.org/reference/functions/register_sidebar/).
* **supports**: Equivalent to the [add\_theme\_support()](https://developer.wordpress.org/reference/functions/add_theme_support/) function.
* **templates**: Handles the custom templates. Define your templates inside this file by providing a `key` or `key/value` pairs.
* **theme**: Main configuration file. You can specify your own class aliases inside this file.

> Details on how to modify and use those configurations are explained inside each file.

Project structure
-----------------

Open your `my-project-name` folder. You should find the following files and directories:

```php
+-- app/
|    +-- Console
|    +-- Exceptions
|    +-- Forms
|    +-- Hooks
|    +-- Http
    |    +-- Controllers
|    +-- Mail
|    +-- Providers
|    +-- Widgets
+-- bootstrap/
+-- config/
|    +-- app.php
|    +-- ...
|    +-- wordpress.php
+-- database/
+-- htdocs/
|   +-- cms/
|   +-- content/
|   +-- index.php
|   +-- wp-config.php
+-- resources/
|    +-- languages/
|    +-- views/
+-- routes/
|    +-- console.php
|    +-- web.php
+-- storage/
+-- tests/
+-- vendor/
+-- .env
+-- composer.json
+-- console
+-- wp-cli.yml
```

### The public directory

The `app` directory is where you store your application core code and logic.

The `bootstrap` directory is where the framework stores cache files

The `config` directory is where you defined your application configuration per environment as well as the shared one. By default, the framework comes with `local.php` and `production.php` environment configuration files inside the `config/environments` folder.

The `htdocs` folder is the **web root** directory. Because WordPress is defined as a dependency, the framework installs the latest WordPress version inside the `cms` folder.

The framework also modifies the default `wp-content` location. Your WordPress plugins and themes should all be installed inside the `content` directory.

The `library` directory contains Themosis custom classes that handle environment loading. It's a framework dependency and may contain other classes to help bootstrap the application.

The `storage` directory is used to store pre-compiled view files and other temporary files. Since release 1.3, the storage directory stores compiled Blade and Twig views.

**Note:** If you install the framework in a "classic" WordPress project, make sure to add a `storage`, `storage/views` and `storage/twig` directories inside your `wp-content` folder.

> Your project will have a `vendor` directory installed. This `vendor` directory is created by default by Composer. It stores package dependencies used by the Themosis framework.