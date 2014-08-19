Configuration
=============

1. Introduction
2. List of configuration files

1.Introduction
--------------

Most of the configuration files are stored in the `app/config` folder of the `themosis-theme` theme.

Sometimes you may want to access configuration values at run-time. But you're constrained to only access the values of the `application.config.php`.

### Access application configuration values

```php
$textdomain = Application::get('textdomain');
```

**Note:** You can't set/modify configuration values at run-time. Configuration values are read only.

Here are complete lists of the configuration files.

2.List of the configuration files
---------------------------------

* **application**: Main configuration file. The only one you can access at run-time.

* **constants**: Allow you to define constant variables.

* **errors**: Control display of PHP errors. By default, set for production environment.

* **images**: Allow you to register image sizes.

* **models**: Define the model classes you want to autoload.

* **controllers**: Define the controller classes you want to autoload.

* **menus**: Allow you to define custom navigation menus. More information in the [WordPress codex](http://codex.wordpress.org/Navigation_Menus).

* **sidebars**: Help you register your sidebars for your website/application. Uses the same arguments found in the [WordPress codex: register_sidebar()](http://codex.wordpress.org/Function_Reference/register_sidebar).

* **supports**: Equivalent to the [add\_theme\_support()](http://codex.wordpress.org/Function_Reference/add_theme_support) function.

* **templates**: Handles the custom page template system of the framework. Define your templates inside this file by providing a `key` name only.

Next
----