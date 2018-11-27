Application Structure
=====================

- [Introduction](#introduction)
- [The root directory](#the-root-directory)
- [Development recommendation](#development-recommendation)
    - [Routing](#routing)
    - [Theme](#theme)
    - [Extend WordPress](#extend-wordpress)
- [The app directory](#the-app-directory)
    - [The Console directory](#the-console-directory)
    - [The Exceptions directory](#the-exceptions-directory)
    - [The Http directory](#the-http-directory)

Introduction
------------

The Themosis framework application structure is now following most of the Laravel framework one, at least on its root directory.

WordPress is defined as a dependency and installed on its own `htdocs/cms` public sub-directory.

Just like previous versions of the Themosis framework, you can still develop your custom theme for the front-end/user facing content and custom plugins in order to extend WordPress and its administration.

However we have brought a new central `app` directory to the stack to help you better manage your code.

On this guide, we'd like to give you some details about the choices made for the directory structure and give you some development recommendations for your custom WordPress application. But of course, you're free to organize your application however you like.

The root directory
------------------

When opening your application in your code editor or IDE, you should find the following files and directories:

```php
+-- app/
|  +-- Console
|  +-- Exceptions
|  +-- Forms
|  +-- Hooks
|  +-- Http
   |  +-- Controllers
|  +-- Mail
|  +-- Providers
|  +-- Widgets
+-- bootstrap/
+-- config/
|  +-- app.php
|  +-- ...
|  +-- wordpress.php
+-- database/
+-- htdocs/
|  +-- cms/
|  +-- content/
|  +-- index.php
|  +-- wp-config.php
+-- resources/
|  +-- languages/
|  +-- views/
+-- routes/
|  +-- console.php
|  +-- web.php
+-- storage/
+-- tests/
+-- vendor/
+-- .env
+-- composer.json
+-- console
+-- wp-cli.yml
```

The biggest change to the directory structure is the adoption of the `app` directory in order to develop your application.

The `app` directory stores all your classes in order to extend or customize your application. More about the [app folder](#the-app-directory) below.

The `bootstrap` directory contains the `app.php` file which bootstraps the application. It also holds a `cache` directory containing generated files by the framework and Laravel packages.

The `config` directory contains all the global or shared configurations for your application.

The `database` directory stores your migration and seeder files.

The `htdocs` directory is your web server public directory.

The `resources` directory stores your shared views as well as language translations files.

The `routes` directory holds your application routes.

The `storage` directory stores framework temporary files, logs, sessions, and any custom files your application need to handle out of the public folder.

The `tests` directory stores all your unit tests.

The `vendor` directory is created by Composer and holds all your application dependencies.

Development recommendation
--------------------------

This is not a "best practice" chapter but some explanations on the changes made to the structure of the Themosis framework.

### Routing

Like for every modern framework, it starts by defining routes. On the previous versions of the Themosis framework, a `routes.php` file was defined into the `resources` directory of the theme. From that file, you list all routes for your application.

Since release 2.0 of the Themosis framework, we decided to move the routes file, just like in Laravel, to the application `routes` directory instead. This change avoid the mix of routes definitions between the file stored in your theme and any other routes file stored in your custom plugin.

From now on, all routes for your application should be defined into the `routes/web.php` file stored at project root.

#### A plugin case for routing

The new plugin structure provides a `routes.php` file at plugin root in order to let you define custom routes. This is still useful, especially if your intention is to share code across multiple projects.

### Theme

The new theme structure mainly handles front-end assets and views of your application.

Just like any classic WordPress theme is doing, the new theme provided by the Themosis framework, is to simply render the content of your application - the HTML. For this, we have moved the `views` directory at theme root and provided default view files based on the [Underscore - _S](https://underscores.me/) theme.

If you feel like you need to customize your application by changing something into the WordPress administration, providing some shared utilities or simply adding some logic, we suggest you to put it into the new `app` directory located at project root, or develop a custom plugin or a custom PHP package.

The new theme is also configured with [Laravel Mix](https://laravel-mix.com/) and is already setup to handle your custom Javascript and Sass files.

Please read the [theme guide]({{url}}/structure) for more details.

#### The root views directory

You could provide views for your entire application through the `resources/views` directory located at application root. However we recommend to use it for shared views or views used to customize the WordPress administration.

### Extend WordPress

In order to extend WordPress, the Themosis framework provides a new mechanism based on Hookable classes. Those classes allow you to extend WordPress and provides a `register()` method, just like Service Providers.

When using Hookable classes, you have access to all WordPress functions and APIs. Those classes are stored in the `app/Hooks` directory.

By default, the framework contains 3 Hookable classes responsible to modify the behavior of WordPress, load core assets to the WordPress administration and register WordPress widgets:
- _Application.php_: setup the application locale, provide some framework utilities and load core framework assets.
- _Mail.php_: configure the WordPress PHPMailer class instance based on global mail configuration.
- _Widgets.php_: sample class you can use to register custom widgets.

> If you provide features that do not need WordPress functions or classes, please use a Service Provider. Read the [Service Provider guide]({{url}}/providers) for more information.

Please read the [Hook guide]({{url}}/hook) to learn more about the new Hookable API.

The app directory
-----------------

As mentioned earlier, the Themosis framework now provides a unique and central `app` directory where you can store your application code.

The `app` directory is where you store your application controllers, models, commands, forms, hooks, widgets and any other classes your application might need to work. The directory is namespaced under `App` and is autoloaded by Composer using the [PSR-4 autoloading standard.](https://www.php-fig.org/psr/psr-4/)

The `app` directory contains many sub-directories that are added when you're using the `make` command from the Console CLI tool provided by the framework.

However here is a list of the default subdirectories you may find under the `app` folder:

### The Console directory

The `Console` directory contains all of the custom Console commands for your application. These commands may be generated using the `make:command` command. This directory also houses your console kernel, which is where your custom Console commands are registered and your [scheduled tasks]({{url}}/scheduling) are defined.

### The Exceptions directory

The `Exceptions` directory contains your application's exception handler and is also a good place to place any exceptions thrown by your application. If you would like to customize how your exceptions are logged or rendered, you should modify the `Handler` class in this directory.

### The Forms directory

The `Forms` directory contains the custom forms of your application. Please read the [form guide]({{url}}/form) for more information.

### The Hooks directory

The `Hooks` directory contains all hookable classes used to customize or extend WordPress. Please read the [hook guide]({{url}}/hook) for more information.

### The Http directory

The `Http` directory contains your controllers, middleware (and form requests). Almost all of the logic to handle requests entering your application will be placed in this directory.

### The Mail directory

This directory does not exist by default, but will be created for you if you execute the `make:mail` Console command. The `Mail` directory contains all of your classes that represent emails sent by your application. Mail objects allow you to encapsulate all of the logic of building an email in a single, simple class that may be sent using the `Mail::send` method.

### The Providers directory

The `Providers` directory contains all of the [service providers]({{url}}/providers) for your application. Service providers bootstrap your application by binding services in the service container, registering events, or performing any other tasks to prepare your application for incoming requests.

In a fresh Themosis application, this directory will already contain several providers. You are free to add your own providers to this directory as needed.

### The Widgets directory

The `Widgets` directory contains all of your WordPress widgets. You can create a new widget by using the `make:widget` Console command and then register it within the `Widgets` hookable class.

Next
----
Read the [framework guide]({{url}}/framework)