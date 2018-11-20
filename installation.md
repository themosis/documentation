Installation
============

- [Requirements](#requirements)
- [Install Composer](#install-composer)
- [Install Themosis framework](#install-themosis-framework)
- [Install the theme](#install-the-theme)
- [Project structure](#project-structure)

Requirements:
-------------

The framework has one system requirement before getting started:

- PHP >= 7.1

Otherwise the server requirements are the same as for WordPress. [Check here](https://wordpress.org/about/requirements/) for default WordPress requirements.

Install Composer
----------------

The Themosis framework uses [Composer](https://getcomposer.org/) to manage its dependencies and easily load its files. Please follow the instructions [here](https://getcomposer.org/doc/00-intro.md) in order to install Composer either locally or globally on your computer.

On Windows, you can use the Composer [Windows installer](https://getcomposer.org/Composer-Setup.exe).

> We recommend you to install Composer globally on your system.

Install Themosis framework
--------------------------

Open your `Terminal` or `Console` and execute the following command:

```bash
composer create-project themosis/themosis my-project-name
```

This will create a directory called `my-project-name` on your computer and automatically download the latest WordPress version along with the latest Themosis framework version and its dependencies.

Install the theme
-----------------

By default, the default theme is no longer installed using Composer.

Instead, a CLI tool is now bundled with the framework and installed at project root. In order to install a fresh theme, run the following command from the terminal:

```bash
php console theme:install
```

The command will setup your theme based on the provided name, setup the theme `style.css` file headers, the theme text domain and set it as the default theme for your WordPress application.

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

### Installation

Habitually, you define your environment variables like database name, database user, authentication salts and other WordPress configurations inside the `wp-config.php` file.

With the Themosis framework, those configurations are extracted from the web root and located in the parent folder, at project root. The framework allows you to define multiple environments: local, production, staging,... and to specify configuration and access per environment.

> Follow the **Environment** guide and see how to **install your WordPress application** on a local and production environment/server.

Next
----
Read the [environment setup guide]({{url}}/environment)
