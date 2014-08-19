Framework
=========

1. Introduction
2. Structure
3. Start building your application

1.Introduction
--------------

The Themosis framework is a pre-configured stack to develop WordPress website and application. It comes with a plugin `themosis-framework` which contains the APIs and a theme `themosis-theme` where you'll write your application/website.

> You can modify the name of the `themosis-theme` folder to better match your project (Note: you can also remove the composer dependency statement **themosis/theme** in your composer.json file).

Let's take a look at the framework plugin and theme structures.

2.Structure
------------

### Plugin: themosis-framework

The plugin only contains the APIs to help you build and develop your WordPress website or application.

Once the plugin is activated, you won't find any UI elements displayed in your WordPress administration.

The plugin gives you classes to easily create or handle: custom post types, metaboxes, custom fields, custom pages, settings, taxonomies, assets, ajax, controllers, views, models, routes and more.

### Theme: themosis-theme

> You can change the `themosis-theme` folder name to match your project.

The `themosis-theme` is stored in the `htdocs/content/themes/` folder of your project. The theme contains an `app` directory. This is the main folder where you write your code for building your application/website.

Let's take a look inside this `app` folder:

- **admin/**: This folder is an extension of the classic `functions.php` file. You can add as many files as you want at its root. As the name suggests, this is where you'll write your code in order to shape the WordPress administration and set your data: custom post types, metaboxes, custom fields, pages,... **Note:** The framework will load your files in alphabetical order. An `application.php` file is loaded by default.

- **assets/**: Insert all your stylesheets, javascripts, images and other public assets inside this folder.

- **config/**: Contains the website/application configuration files:  `application`, `constants`, `controllers`, `images`, `menus`, `models`, `sidebars`, `supports`, `templates`. Read the configuration guide for more information.

- **controllers/**: This folder stores all your controller classes.

- **models/**: Stores your model classes.

- **storage/**: Stores all temporary views. **Note:** This directory must be writable.

- **tests/**: Stores your PHPUnit tests.

- **views/**: This folder allows you to organize your views - HTML. You can point to sub-directories using a dot syntax.

- **widgets/**: Contains your widget classes.

- **routes.php**: This file handles your website routes. The route system behaves like an "if" statement on steroids. Read the routing guide for more information.



3.Start building your application
---------------------------------

In order to build your application or website, you write your code in the `themosis-theme/app` folder only.

Let's start by looking at the route API.

Next
----
Read the [routing guide](https://github.com/themosis/documentation/blob/master/routing.md)
