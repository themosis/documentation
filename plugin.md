Plugin development
==================

> Documentation in progress...

- [Introduction](#introduction)
- [Installation](#installation)
    - [Install as a mu-plugin](#install-as-a-mu-plugin)
- [Plugin structure](#plugin-structure)
- [Managing assets](#managing-assets)
- [Translations](#translations)
- [Notes](#notes)
    - [Routes](#routes)
    - [Views](#views)
    - [Themosis facades](#themosis-facades)

Introduction
------------

The framework provides a plugin boilerplate in order to help you build custom features for your application by leveraging the WordPress plugin API.

The plugin boilerplate uses a similar structure as the theme and lets you develop WordPress plugins using manageable and scalable methods.

Installation
------------

The framework comes with a command line tool at its root directory. In order to create a new plugin, open your terminal or console, go to your application root directory and run the `plugin:install` command like so:

```bash
php console plugin:install plugin-name
```

The command will then ask you to fill in some information before installing the plugin:

- **Description**: the plugin description text.
- **Author**: the plugin author name.
- **Text domain**: default value based on the plugin name but you can provide a custom one.
- **Domain variable**: default to `PLUGIN_TD`. This is the plugin constant name to define for use inside your code for translations. We highly recommend you to provide a custom name in uppercase letter (constant).
- **Plugin prefix**: a prefix name used to setup the plugin configuration files and avoid conflicts between plugins. Keep the prefix in lowercase.
- **PHP namespace**: the PHP namespace of your plugin. We recommend you to use a `Tld\Domain\PluginName` syntax. For example, if your plugin is for an application hosted on the `book.store` domain and you're building a book manager, the namespace value could be `Store\Book\Manager`

After filling in the necessary information, the command will install your new plugin into the `htdocs/content/plugins/plugin-name` directory.

The plugin is not activated by default, you must log in into the WordPress administration and activate the plugin manually or using the `WP-CLI`.

### Install as a mu-plugin

You can also install your plugin into the `mu-plugins` directory when calling the `plugin:install` command. Use the `--mu` option to inform the command to install it as a MU Plugin:

```bash
php console plugin:install plugin-name --mu
```

The above command will install the `plugin-name` inside the `htdocs/content/mu-plugins/plugin-name` directory.

///

Notes
-----

### Routing

You can define default routes from your plugin `routes.php` file. This is useful for example if you would like to provide default views/templates without the need for the theme to handle them.

The theme `routes.php` file can override routes defined inside the plugin. This behavior lets a theme developer customize the template.

### Views

Default plugin views are stored inside its `resources/views` directory. Imagine the following scenario where your plugin stores those 2 views:

- resources/views/layout/main.twig
- resources/views/store/shop.twig

Now if your theme also has similar views stored in its `resources/views` directory:

- resources/views/layout/main.twig
- resources/views/store/shop.twig

**What would be the result?**

If a theme calls its view like so: `view(store.shop)`, it's the plugin view that is returned and not the one stored inside the theme. In order to avoid view conflicts, we recommend you to "namespace" your plugin views directory like so:

```php
resources/views/
+-- tld/
    +-- domain/
        +-- plugin/
            +-- layouts/
            |   +-- main.twig
            +-- store/
            |   +-- shop.twig
```

Creating sub-folders using your domain name, tld extension and plugin name prevent any conflicts between plugins and theme views. So based on our example, our `themosis-documentation` plugin would call its view like so: `view('com.themosis.documentation.store.shop');`

### Themosis facades

Since it is mandatory to use PHP namespace for all plugin classes, you can't call directly the Themosis framework facades like inside the theme `admin` files. In order to use the APIs provided by the framework, you first need to import it.

For example, let's image that we want to define a custom post type of `books` from our plugin `resources/admin/plugin.php` default file, we would need to code it like so:

```php
<?php

use Themosis\Facades\PostType;

PostType::make('books', 'Books', 'Book')->set();
```

> This is also similar from "namespaced" classes of your custom theme.

Here is a list of available facades you can import:

- Themosis\Facades\Action
- Themosis\Facades\Ajax
- Themosis\Facades\Asset
- Themosis\Facades\Blade
- Themosis\Facades\Config
- Themosis\Facades\DB
- Themosis\Facades\Field
- Themosis\Facades\Filter
- Themosis\Facades\Form
- Themosis\Facades\Html
- Themosis\Facades\Input
- Themosis\Facades\Loop
- Themosis\Facades\Metabox
- Themosis\Facades\Page
- Themosis\Facades\PostType
- Themosis\Facades\Route
- Themosis\Facades\Section
- Themosis\Facades\Taxonomy
- Themosis\Facades\User
- Themosis\Facades\Validator
- Themosis\Facades\View