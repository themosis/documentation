Plugin development
==================

> Documentation in progress...

- [Introduction](#introduction)
- [Installation](#installation)
    - [Install as a mu-plugin](#install-as-a-mu-plugin)
- [Plugin structure](#plugin-structure)
    - [The assets directory](#the-assets-directory)
    - [The config directory](#the-config-directory)
    - [The dist directory](#the-dist-directory)
    - [The inc directory](#the-inc-directory)
    - [The languages directory](#the-languages-directory)
    - [The resources directory](#the-resources-directory)
    - [The views directory](#the-views-directory)
    - [Plugin routes](#plugin-routes)
- [Managing assets](#managing-assets)
    - [Install Laravel Mix](#install-laravel-mix)
    - [Configure assets](#configure-assets)
    - [Compile assets](#compile-assets)
    - [Asset version](#asset-version)
- [Plugin manager](#plugin-manager)
    - [Get the plugin manager](#get-the-plugin-manager)
    - [Get a plugin header](#get-a-plugin-header)
    - [Get plugin path](#get-plugin-path)
    - [Get plugin url](#get-plugin-url)
    - [Get plugin configuration](#get-plugin-configuration)
- [Translations](#translations)

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

Plugin structure
----------------

```php
+-- assets
+-- config
+-- dist
+-- inc
+-- languages
+-- resources
+-- views
+-- package.json
+-- plugin-name.php
+-- routes.php
+-- webpack.mix.js
```

### The assets directory

The `assets` directory contains all your JavaScript and CSS files. There is a default `js/index.js` file as well as a `sass/style.scss` [Sass](https://sass-lang.com/) file.

### The config directory

The `config` directory contains the plugin configuration file. The plugin is only bundled with a prefixed `plugin.php` configuration file. The prefix used for the configuration file is the `Plugin prefix` option value defined during installation. This prefix is used to avoid conflicts between plugins when you try to fetch a specific configuration file. See the [plugin manager](#plugin-manager) for more details on how to use your configuration files.

The bundled configuration file helps you define your plugin resources directory path for class autoloading, the list of service providers to load as well as the paths to its views directory.

### The dist directory

The `dist` directory contains your compiled assets. Compiled assets are then enqueued by the [Asset]({{url}}/asset) API. Asset compilation is managed by [webpack](https://webpack.js.org/) through the [Laravel Mix](https://laravel-mix.com/) tool. You can read more about managing the assets [below](#managing-assets).

### The inc directory

The `inc` directory is a folder that extends the base plugin file. You can insert as many PHP files as you want into the folder and all files will be automatically included. You can even nest PHP files within subfolders.

### The languages directory

The `languages` directory contains the PO template file for your plugin. You can load it with the [PoEdit](https://poedit.net/) software and start translating your plugin.

### The resources directory

The `resources` directory is the plugin namespaced directory. You can organize your plugin PHP Class into the folder by following the [PSR-4](https://www.php-fig.org/psr/psr-4/) specification. The namespace used for your plugin has been defined during installation and is pointing to the `resources` directory. By default, it contains a `Providers` directory that holds the plugin `RouteServiceProvider` class.

### The views directory

The `views` directory contains all your plugin view files. We recommend to organize your plugin views in sub-directories using at least a unique prefix in order to avoid conflicts with view paths from another plugin, the theme or your application root views.

### Plugin routes

The plugin provides a `routes.php` file at its root. You can extend the list of available routes of your application as well as providing custom endpoints from your plugin. The `RouteServiceProvider` class, responsible to load your plugin routes, is also configured to handle plugin controllers and their namespace. In order to work with controllers in your plugin, simply create a `Controllers` directory inside the `resources` folder. Controllers will be autoloaded like expected.

Managing assets
--------------

In order to manage your assets, the plugin is now bundled with [Laravel Mix](https://laravel-mix.com/). Mix is an elegant wrapper around [Webpack](https://webpack.js.org/) for the 80% use case.

Laravel Mix is the only front-end dependency defined in the plugin `package.json` file. With Laravel Mix, you'll be able to compile your JavaScript files as well as your stylesheets and any other required assets for your plugin.

### Install Laravel Mix

Before installing Laravel Mix, you need to make sure to have the following tool installed on your machine:

- [NodeJs](https://nodejs.org/en/)

You can handle your node packages using the default `npm` handler provided by NodeJs or you can also install [Yarn](https://yarnpkg.com/en/) for managing them.

Once NodeJs is installed, open a terminal or console and go to your plugin root and run the following command:

```bash
npm install
```

or if you're using Yarn:

```bash
yarn install
```

The above command will install Laravel Mix and all its dependencies.

### Configure assets

In order to configure Laravel Mix, you have to edit the `webpack.mix.js` file located at plugin root. Here is the default configuration provided by the plugin:

```javascript
let mix = require('laravel-mix');

mix.js('assets/js/index.js', 'dist/')
    .sass('assets/sass/style.scss', 'dist/');

```

Webpack is compiling the `assets/js/index.js` JavaScript file using the `mix.js()` method and is also compiling the `assets/sass/style.scss` Sass file provided by the plugin using the `sass()` method.

Laravel Mix provide multiple methods to compile ReactJs components, VueJs components, Less, Stylus, and many more as well as working with BrowserSync, ...

For more configuration options, please read the [official documentation](https://laravel-mix.com/).

### Compile assets

The plugin `package.json` file also provides a bunch of scripts/commands in order to help you work with your assets.

#### For local development

For local development, you'll mainly work with a mix of the three following commands: `dev`, `hot` and `watch`:
```bash
npm run dev

npm run watch

npm run hot
```

#### For production

When you're ready to publish your application, run the `production` command in order compile your assets for production:

```bash
npm run production
```

### Asset version

When enqueueing assets, it is common to provide a version number in order to provide a browser cache hint. The version number of the default plugin assets is set based on the plugin `Version` header defined into the plugin root file.

In order to reset browser cache for your assets after a modification, simply update the `Version` header value from the plugin root file.

```css
/*
Plugin Name: Themosis
...
Version: 1.0.1
...
Text Domain: themosis
*/
```

Plugin assets are not enqueued by default. In order to do so, we recommend you to create an `AssetServiceProvider` into the `resources/Providers` directory of your plugin and call the Asset API like so:

```php
<?php
namespace Tld\Domain\PluginName\Providers;

use Illuminate\Support\ServiceProvider;
use Themosis\Support\Facades\Asset;

class AssetServiceProvider extends ServiceProvider
{
    public function register()
    {
        /** @var PluginManager $plugin */
        $plugin = $this->app->make('wp.plugin.plugin-name');

        Asset::add('plugin_styles', 'css/style.css', [], $plugin->getHeader('version'))->to('front');
        Asset::add('plugin_js', 'js/index.min.js', [], $plugin->getHeader('version'))->to('front');
    } 
}
```

> The plugin is manage by a `PluginManager` class. See how the plugin `version` value is retrieved from its root file header.

Plugin manager
--------------

The framework is handling a plugin through a `PluginManager` class. The manager is responsible to provide utilities in order to work with the plugin as well as registering its configuration.

You can find below a list of methods available in order to work with your plugin.

### Get the plugin manager

If you need to work inside the plugin root file, the `PluginManager` class instance is already provided to you through the `$plugin` variable.

The `PluginManager` class instance is also registered into the application service container under the abstract name `wp.plugin.plugin-name` where `plugin-name` must be replaced by your plugin unique name. Here is an example on how to retrieve it from a plugin service provider:

```php
<?php
namespace \Tld\Domain\Plugin\Providers;

use Illuminate\Support\ServiceProvider;

class MyServiceProvider extends ServiceProvider
{
    public function register()
    {
        $plugin = $this->app->make('wp.plugin.plugin-name');
    }
}
```

It is also possible to retrieve the plugin manager instance by using the `app()` helper function:

```php
$theme = app('wp.plugin.plugin-name');
```

### Get a plugin header

Sometimes, you may need to retrieve information from the plugin headers defined inside its root file. In order to get a header value, use the `getHeader` method like so:

```php
$plugin = app('wp.plugin.plugin-name');
$version = $plugin->getHeader('version');
```

Here is the list of available headers you can retrieve from the plugin root file:

```php
$name = $plugin->getHeader('name');
$plugin_uri = $plugin->getHeader('plugin_uri');
$plugin_prefix = $plugin->getHeader('plugin_prefix');
$plugin_namespace = $plugin->getHeader('plugin_namespace');
$author = $plugin->getHeader('author');
$author_uri = $plugin->getHeader('author_uri');
$description = $plugin->getHeader('description');
$version = $plugin->getHeader('version');
$license = $plugin->getHeader('license');
$license_uri = $plugin->getHeader('license_uri');
$text_domain = $plugin->getHeader('text_domain');
$domain_path = $plugin->getHeader('domain_path');
$domain_var = $plugin->getHeader('domain_var');
$network = $plugin->getHeader('network');
```

### Get plugin path

If you need to get the plugin path or a path of a sub-directory of your plugin, you can use the `getPath` method like so:

```php
$plugin_root_path = $plugin->getPath();

$includes = $plugin->getPath('inc');
```

### Get plugin URL

If you need to get the plugin URL or a URL of a sub-directory of your plugin, you can use the `getUrl` method like so:

```php
$plugin_url = $plugin->getUrl();

$plugin_image = $plugin->getUrl('dist/images/logo.png');
```

### Get plugin configuration

You can get a plugin configuration by using the `Config` facade or the `config()` helper function like so:

```php
$params = Config::get('prefix_plugin');

$params = config('prefix_plugin');
```

> You need to make sure to remember your plugin prefix in order to fetch its configuration files.

The plugin manager provides a `config()` method that takes care of the prefix for you. So if you have a plugin configuration file with a full name of `prefix_services.php`, you can get its values like so:

```php
$plugin = app('wp.plugin.plugin-name');

$services = $plugin->config('services');
```

Translations
------------

The plugin is translation ready and provides a `languages` directory with a `.po` template file. The PO template file name is setup during plugin installation and follows the `plugin-name-en_US.po` syntax.

When writing code for your plugin that must be translated, the plugin manager is setting up a PHP constant for you to use inside gettext functions. The constant name is defined during plugin installation and is registered as the `Domain Var` plugin header. If you omit to define a constant name at installation, its default name is `PLUGIN_TD`.

> If you forget to provide a custom constant name at installation, simply update the `Domain Var` plugin header.

The constant value is automatically defined based on plugin name during installation but you can easily change it by changing the `Text Domain` header value.

Finally you may use the plugin constant in your views like so:

```php
// From a Blade view
<button>{{ __("Read more", PLUGIN_TD) }}</button>

// From a Twig view
<button>{{ __("Read more", constant('PLUGIN_TD')) }}</button>
```

Based on defined locale from WordPress, the plugin is automatically loading the corresponding translation file for you.

> We recommend the use of [PoEdit](https://poedit.net/) in order to update and translate your files. Check the [Twig]({{url}}/twig) or [Blade]({{url}}/blade) guides in order to configure PoEdit for your views.