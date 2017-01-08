Plugin development
==================

- [Introduction](#introduction)
- [Installation](#installation)
- [Configuration](#configuration)
    - [Composer](#composer)
    - [Plugin name and properties](#plugin-name-and-properties)
- [Namespace](#namespace)
- [Refactor the defaults](#refactor-the-defaults)
- [Notes](#notes)
    - [Routes](#routes)
    - [Views](#views)
    - [Themosis facades](#themosis-facades)

Introduction
------------

Since release 1.3.0, the Themosis framework provides a plugin boilerplate in order to help you develop custom WordPress plugins that can leverage Themosis framework APIs.

The plugin boilerplate provides a similar folder structure borrowed from the `themosis-theme` and let you develop WordPress plugins using manageable and scalable methods.

Installation
------------

In order to start developing custom plugins using the Themosis framework, you fist need to download a copy of the plugin boilerplate from our GitHub account:

1. From your browser, go to [our plugin boilerplate](https://github.com/themosis/plugin) repository.
2. Click the `Clone or download` green button on the right of the screen.
3. Choose `Download ZIP` and save the plugin file on your machine.
4. Extract the files into your project `htdocs/content/plugins` directory.
5. Rename your plugin boilerplate folder with a name that reflects your extension purpose
 
For this example, let's imagine a custom plugin that handles an application documentation and is called `themosis-documentation`.
If you have correctly followed the previous steps, you have the plugin boilerplate files into the following directory of your Themosis framework application: `htdocs/content/plugins/themosis-documentation/`

Let's configure our plugin.

Configuration
-------------

You could activate the plugin now but its files names and properties do not correspond yet to our planned extension. Please read the following steps in order to properly configure your custom plugin and make it work with the Themosis framework.

### Composer

The plugin boilerplate contains a default `composer.json` file with basics informations. Please first update the `composer.json` file properties in order to define your plugin as a correct PHP package as well. If you require PHP packages, you can define them here so when you install your complete Themosis framework application, any plugin dependencies are loaded into application root `vendor` directory and not with your plugin.

Inside the `composer.json` file, leave the `type`property set to `wordpress-plugin`. This helps composer to install your plugin in the correct folder when creating a project with your extension.

> Make sure to add your plugin `vendor` directory to the `.gitignore` file as you won't need to track those dependencies.

### Plugin name and properties

Following WordPress recommendations, let's first update our plugin root file name and its inner properties.

1. Open your extension into your favorite IDE or code editor.
2. At plugin root, rename the default `plugin-name.php` file as your plugin folder name; In our example, this file is renamed as `themosis-documentation.php`.
3. Open your new plugin root file (in our case `themosis-documentation.php` file).
4. Update comments located at the top of the file (Plugin Name, Plugin URI, Description, Version,...) except the `Domain Path` which is already set correctly for you.

#### Set plugin textdomain constant

In each plugin, we have set a default textdomain constant for use into all your gettext functions calls. Check around the plugin root file line #35 and update the placeholder constant `MY_PLUGIN_TD`. For example, based on our `themosis-documentation` website, we'll update this constant statement like so:

```php
defined('THEMOSIS_DOCS_TD') ? THEMOSIS_DOCS_TD : define('THEMOSIS_DOCS_TD', 'themosis-documentation');
```

##### Find and replace

In your plugin root file, **find and replace** all instances of `MY_PLUGIN_TD` by your custom plugin textdomain constant.

> Make sure to set your constant value ("themosis-documentation" in our example) as the same value as top file comment `Text Domain: themosis-documentation`.

#### Set plugin properties

Around line #45 of your custom plugin root file, you'll find a `$vars` array containing plugin configuration properties:

```php
$vars = [
    'slug' => 'plugin-key',
    'name' => 'Plugin Name',
    'namespace' => 'tld.domain.plugin',
    'config' => 'tld_domain_plugin'
];
```

The `slug` property is a plugin key name. This is used by your theme in order to support the plugin. The `name` property is your plugin display name used on admin notice. 

The `namespace` property is the most **important** one. It will help define a custom path to your plugin and avoid conflicts with others plugins. Like the placeholder value suggests, we recommend you to use your domain name, its tld extension and finally the plugin name, all separated with a dot `.`.

Finally the `config` property is your plugin configuration files names prefix to use. Same as the `namespace` property except that you separate each term by an underscore `_`. This is also **important** in order to avoid configuration file names conflicts.

Based on our documentation example, here is an example on how should look like our `$vars` array properties:

```php
$vars = [
    'slug' => 'themosis-documentation',
    'name' => 'Themosis Documentation',
    'namespace' => 'com.themosis.documentation',
    'config' => 'com_themosis_documentation'
];
```

Namespace
---------

When developing custom plugins, it is **mandatory** to work with a PHP namespace. This will help prevent class conflicts with all your plugins.

Before defining class namespace, we first need to update the file names of the given default configuration files stored inside the `resources/config` directory of our custom plugin based on the `config` plugin property we just set.

The `resources/config` folder stores by default those 2 files:

1. _tld\_domain\_plugin\_loading.config.php_: Handle PSR-4 autoloading rules of your custom plugin
2. _tld\_domain\_plugin\_providers.config.php_: Handle your custom plugin Service providers

Based on our example, we will rename those files like so:

1. _com\_themosis\_documentation\_loading.config.php_
2. _com\_themosis\_documentation\_providers.config.php_

### Update PSR-4 loading rules

Open your renamed `tld_domain_plugin_loading.config.php` file and set the PHP namespace and paths for the pre-defined service providers and controllers like so (still based on our example):

```php
return [
    'Com\\Themosis\\Documentation\\Services\\' => themosis_path('plugin.com.themosis.documentation.resources').'providers',
    'Com\\Themosis\\Documentation\\Controllers\\' => themosis_path('plugin.com.themosis.documentation.resources').'controllers'
];
```

> The paths contains the plugin `namespace` string defined on your plugin root file inside the `$vars` variable. Make sure to correctly define it here as well.

### Update service providers

Open your renamed `tld_domain_plugin_providers.config.php` file and update the default RoutingService class namespace. Base on our example, the default `Tld\Domain\Plugin\Services\RoutingService::class` would be:

```php
return [
    Com\Themosis\Dcumentation\Services\RoutingService::class
];
```

Refactor the defaults
---------------------

As a last step, the plugin boilerplate comes with a sample controller class and a default RoutingService to refactor:

1. Open the default `Sample.php` controller file stored in the `resources/controllers` directory.
2. Update the PHP namespace at the top to use your previously defined namespace inside the `tld_domain_plugin_loading.config.php` file. In our example the namespace would be `namespace Com\Themosis\Documentation\Controllers;`

The `Sample` controller is there to give an example of a properly defined controller to use inside your plugin. We recommend you to rename the `Sample` class controller for your needs.

### Update plugin routes namespace

The default RoutingService class, stored inside the `resources/providers` directory, loads the plugin `resources/routes.php` file for you and define its controllers namespace and path. Update the route namespace and path placeholders with your previously defined namespace from your `tld_domain_plugin_loading.config.php` and plugin root files. Based on our example, the service `register()` method would look like this:

```php
public function register()
{
    Route::group([
        'namespace' => 'Com\Themosis\Documentation\Controllers'
    ], function () {
        require themosis_path('plugin.com.themosis.documentation.resources').'routes.php';
    });
}
```

You now have a fully configured custom plugin. From the WordPress administration, active your custom plugin and start developing your custom features.

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
+-- tld
    +-- domain
        +-- plugin
            +-- layouts
            |   +-- main.twig
            +-- store
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