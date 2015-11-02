Framework
=========

- Introduction
- Structure
- Start building your application

Introduction
------------

The Themosis framework is a pre-configured stack to develop WordPress website and application. It comes with a plugin `themosis-framework` which contains the APIs and a theme `themosis-theme` where you'll write your application/website code.

> We suggest you to rename the default `themosis-theme` folder name to better match your project (Note: you can also remove the composer dependency statement **themosis/theme** in your composer.json file).

Let's take a look at the framework plugin and theme structures.

Structure
---------

### Plugin: themosis-framework

The plugin only contains the APIs to help you build and develop your WordPress website or application.

> Note: the `themosis-framework` package is installed inside the `mu-plugins` folder. So there is no need to activate it once WordPress is installed.

The plugin gives you classes to easily create or handle: custom post types, metaboxes, custom fields, custom pages, settings, taxonomies, assets, ajax, controllers, views, models, routes and more.

### Theme: themosis-theme

> We suggest you to change the `themosis-theme` folder name to match your project.

The `themosis-theme` is stored in the `htdocs/content/themes/` folder of your project. The theme contains a `resources` directory. This is the main folder where you write your code for building your application/website's theme.

Let's take a look inside this `resource` folder:

- **admin/**: This folder is an extension of the classic `functions.php` file. You can add as many files as you want at its root. As the name suggests, this is where you'll write your code in order to shape the WordPress administration and set your data: custom post types, metaboxes, custom fields, pages,... **Note:** The framework will load your files in alphabetical order. An `actions.php` and `application.php` file are loaded by default.

- **assets/**: Insert all your stylesheets, javascripts, images and other public assets inside this folder.

- **config/**: Contains the theme configuration files:  `application`, `constants`, `images`, `loading`, `menus`, `sidebars`, `supports`, `templates`. Read the [configuration guide](http://framework.themosis.com/docs/configuration/) for more information.

- **controllers/**: This folder stores all your controller classes.

- **models/**: Stores your model classes.

- **views/**: This folder contains your views - HTML. You can point to sub-directories using a dot syntax.

- **widgets/**: Contains your widget classes.

- **routes.php**: This file handles your website routes. The route system behaves like an "if" statement on steroids. Read the [routing guide](http://framework.themosis.com/docs/routing/) for more information.


Start building your application
-------------------------------

Currently, in order to build your application or website, you write your code inside the `themosis-theme/resources` folder only.

Let's start by looking at the route API.

Next
----
Read the [routing guide](http://framework.themosis.com/docs/routing/)
