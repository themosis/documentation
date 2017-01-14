Framework
=========

- [Introduction](#introduction)
- [Structure](#structure)
- [Start building your application](#start-building-your-application)

Introduction
------------

The Themosis framework is a pre-configured stack to develop WordPress website and application. It comes with a plugin, the `themosis-framework` which contains the APIs and a theme `themosis-theme` where you'll write your application/website  front-end code.

Since release 1.3.0, you can also develop WordPress plugins that leverage the Themosis framework API. By default, this plugin boilerplate package is not included when starting a new project. In order to learn more about plugin development, please read the [plugin guide]({{url}}/plugin).

> We suggest you to rename the default `themosis-theme` folder name to better match your project. This also prevents Composer from erasing your custom theme. We also recommend to remove the composer dependency statement **themosis/theme** in your composer.json file located at your project root.

Let's take a look at the framework structure.

Structure
---------

### Plugin: themosis-framework

The plugin only contains the APIs to help you build and develop your WordPress website or application.

> Note: the `themosis-framework` package is installed inside the `mu-plugins` folder. So there is no need to activate it once WordPress is installed.

The plugin gives you classes to easily create or handle: custom post types, metaboxes, custom fields, custom pages, settings, taxonomies, assets, ajax, controllers, views, models, routes and more.

### Theme: themosis-theme

> As previously suggested, we recommend you to change the `themosis-theme` folder name to match your project.

The `themosis-theme` is stored inside the `htdocs/content/themes/` folder of your project. The theme contains a `resources` directory. This is the main folder where you write your code for building your application/website's theme.

Let's take a look inside this `resources` folder:

- **admin/**: This folder is an extension of the classic `functions.php` file. You can add as many files as you want at its root. As the name suggests, this is where you'll write your code in order to shape the WordPress administration and general behavior on the front-end. **Note:** The framework will load your files in alphabetical order. An `actions.php` and `theme.php` files are loaded by default.

- **config/**: Contains the theme configuration files: `constants`, `images`, `loading`, `menus`, `providers`, `sidebars`, `supports`, `templates`, `theme`. Read the [configuration guide]({{url}}/configuration) for more information.

- **controllers/**: This folder stores all your theme's controller classes.

- **models/**: Stores your model classes. **Note:** If you're using the Themosis framework "full-stack", you can leverage the Eloquent ORM. Read the [models guide]({{url}}/models)

- **providers/**: Stores your service providers classes. Since release 1.3.0, the framework takes advantages of the Illuminate Container and Service Provider packages. Read the [providers guide]({{url}}/providers).

- **views/**: This folder contains your views - HTML. You can point to sub-directories using a dot syntax.

- **widgets/**: Contains your widget classes. Read the [widgets guide]({{url}}/widgets) for more information on how to register your widgets.

- **routes.php**: This file handles your theme's routes. Since release 1.3.0, we've implemented the full Illuminate Router package. This means that you can define custom (like in any web framework) and WordPress routes. Read the [routing guide]({{url}}/routing) for more information on how to manage, create custom and WordPress routes.

Finally, one difference is the directory change regarding the **assets**. From now on, there are 2 directories located at theme's root:

- **assets/**: Stores your raw assets like sass, stylus, typescript, js files as well as any Bower components into a child directory called `components`.
- **dist/**: Stores your compiled assets: css, js, images, fonts.

Indeed, the new theme comes bundled with Gulp (4.0), WebPack and configuration files to handle watch and build tasks on Sass, Stylus and JS files.

> For more information regarding theme assets development, please read the [theme guide]({{url}}/theme).

Start building your application
-------------------------------

Until now, you wrote code only inside your theme `resources` directory in order to customize your project. But this has to change.

Since release 1.3.0, we highly recommend you to develop custom plugins when you intend to extend WordPress and let the theme leverage the display.

We've also reinforced the use of PHP namespaces over all the framework.

The following guides will give you code examples on how to use the APIs. And as for now, those code samples do work inside your theme or inside any custom plugins built with the [Themosis Plugin boilerplate]({{url}}/plugin).

As a general rule, we've developed both theme and plugin with a similar folder structure. No matter where you code, theme or plugin, you'll mainly write your code inside their `resources` folder.

Composer support
----------------

By default, the Themosis framework block core WordPress automatic updates except security patches. This is done on purpose as we encourage people to handle application updates and manage their dependencies, plugin and PHP packages, through the use of composer.

As a matter, we highly recommend users to define their PHP packages but as well their WordPress plugin dependencies into the root `composer.json` file of their project.

Plugins, like the WordPress core, should not be modified but extended. Meaning that by default WordPress and plugins should not be tracked with your version control system. This configuration allows for easy and light code management of your WordPress application.

Now, let's get started by looking at the route API.

Next
----
Read the [routing guide]({{url}}/routing)
