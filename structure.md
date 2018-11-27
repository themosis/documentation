Application Structure
=====================

- [Introduction](#introduction)
- [The root directory](#the-root-directory)
- [The app directory](#the-app-directory)

Introduction
------------



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

### The app directory

The Themosis framework now provides a unique and central `app` directory where you can store your custom code. 
The `app` directory is where you store your application core code and logic.

The `bootstrap` directory is where the framework stores cache files

The `config` directory is where you defined your application configuration per environment as well as the shared one. By default, the framework comes with `local.php` and `production.php` environment configuration files inside the `config/environments` folder.

The `htdocs` folder is the **web root** directory. Because WordPress is defined as a dependency, the framework installs the latest WordPress version inside the `cms` folder.

The framework also modifies the default `wp-content` location. Your WordPress plugins and themes should all be installed inside the `content` directory.

The `library` directory contains Themosis custom classes that handle environment loading. It's a framework dependency and may contain other classes to help bootstrap the application.

The `storage` directory is used to store pre-compiled view files and other temporary files. Since release 1.3, the storage directory stores compiled Blade and Twig views.

**Note:** If you install the framework in a "classic" WordPress project, make sure to add a `storage`, `storage/views` and `storage/twig` directories inside your `wp-content` folder.

> Your project will have a `vendor` directory installed. This `vendor` directory is created by default by Composer. It stores package dependencies used by the Themosis framework.