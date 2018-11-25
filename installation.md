Installation
============

- [Requirements](#requirements)
- [Install Composer](#install-composer)
- [Install Themosis framework](#install-themosis-framework)
- [Install the theme](#install-the-theme)
- [Project structure](#project-structure)

Requirements:
-------------

The server requirements are basically the same as for [WordPress](https://wordpress.org/about/requirements/) with the addition of a few ones:

- PHP >= 7.1
- [Intl PHP Extension](http://php.net/manual/fr/intro.intl.php)

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

Instead, a CLI tool is now bundled with the framework and installed at project root. In order to install the new theme, run the following command from the terminal:

```bash
php console theme:install
```

The command will ask the user for a theme name and then download and unzip the theme into the `htdocs/content/themes` directory. The script is also setting up the theme `style.css` file headers, the theme text domain and set it as the default theme for your WordPress application.

Install WordPress
-----------------

Habitually, you define your environment variables like database name, database user, authentication salts and other WordPress configurations inside the `wp-config.php` file.

With the Themosis framework, those configurations are extracted from the web root and located in the parent folder, at project root through the use of a `.env` file.

Follow the [configuration guide]({{url}}/configuration) and see how to install your WordPress application.

Next
----
Read the [configuration guide]({{url}}/configuration)