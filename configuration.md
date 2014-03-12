Configuration
=============

You'll spend most of your time writing your code inside the `themosis` plugin and `themosis-theme` theme.

1. Folder structure
2. Configuration


1. Folder structure
-------------------

On both the `themosis` plugin and `themosis-theme` theme, there is a folder called `app`. This is the main folder where you write your code.

The **plugin** folder structure:

- **/admin**: This folder is an extension of the classic `functions.php` file. You can add as many files as you want. **Note:** the framework will load them in alphabetical order. As the name suggests, this is where you'll write your code in order to shape the WordPress administration and setup your datas: custom post types, metaboxes, custom fields, options, ...
- **/config**: Contains the main configuration files: `application`, `constants`, `errors`
- **/models**: Store your model classes.
- **/widgets**: Contains your widget classes.

The **theme** folder structure:

- **/assets**: Insert all your stylesheets, javascripts, images and other public assets inside this folder.
- **/config**: Contains the theme configuration files: `menus`, `sidebars`, `supports`, `templates`.
- **/controllers**: This folder stores all your controllers.
- **/views**: This folder allows you to organize your views - HTML. You can point to subdirectories using a dot syntax.
- **routes.php**: This file handles your website routes. An "if" statement on streroids. Check the routing guide for more information.

2. Configuration
----------------

Most of the configuration files are stored in the `app/config` folder of either the `themosis` plugin and `themosis-theme` theme.

Sometimes you may want to access configuration values at run-time. But you're constrained to only access the values of the `application.config.php` file stored in the `themosis` plugin.

### Access application configuration values

```php
$textdomain = Application::get('textdomain');
```

**Note:** You can't set/modify configuration values at run-time. Configuration values are read only.

Here are complete lists of the configuration files.

### List of the plugin configuration files:
* **application**: Main configuration file. The only one you can access at runtime.
* **constants**: Allow you to define constants variables to use inside the plugin or theme.
* **errors**: Control display of PHP errors. By default, set for production environment.

### List of the theme configuration files:
* **menus**: Allow you to define custom navigation menus. More infos in the [WordPress codex](http://codex.wordpress.org/Navigation_Menus).
* **sidebars**: Help you register your sidebars for your website/application. Uses the same arguments as in the [WordPress codex: register_sidebar()](http://codex.wordpress.org/Function_Reference/register_sidebar).
* **supports**: Equivalent to the [add\_theme\_support()](http://codex.wordpress.org/Function_Reference/add_theme_support) function.
* **templates**: Handles the custom page template system of the framework. Define yours inside this file by providing a `key` name.

Next
----
Check the [routing guide](https://github.com/themosis/documentation/blob/master/routing.md)