Theme development
=================

- [Introduction](#introduction)
- [Installation](#installation)
- [Theme structure](#theme-structure)
    - [The assets directory](#the-assets-directory)
    - [The config directory](#the-config-directory)
    - [The dist directory](#the-dist-directory)
    - [The inc directory](#the-inc-directory)
    - [The languages directory](#the-languages-directory)
    - [The resources directory](#the-resources-directory)
    - [The views directory](#the-views-directory)
    - [Theme routes](#theme-routes)
- [Managing assets](#managing-assets)
    - [Install Laravel Mix](#install-laravel-mix)
    - [Configure assets](#configure-assets)
    - [Compile assets](#compile-assets)
    - [Asset version](#asset-version)
- [Theme manager](#theme-manager)
    - [Get the theme manager](#get-the-theme-manager)
    - [Get a theme header](#get-a-theme-header)
    - [Get theme path](#get-theme-path)
    - [Get theme URL](#get-theme-url)
- [Translations](#translations)

Introduction
------------

By default, when you create a new project, a theme is no longer installed automatically for your project. If you have followed the installation guide, you must already have a theme installed for your application. If not, please follow the installation step below.

Installation
------------

The framework comes with a command line tool at its root directory. If you need to install a fresh theme for your project, you first need to open your terminal and then go to your application root and run the `theme:install` command like so:

```bash
php console theme:install your-theme-name
```

You have to provide a name for your theme when calling the command. Spaces are not allowed and we also suggest that you keep your theme name in lowercase.

The `theme:install` command will download the latest theme boilerplate, install it under the `htdocs/content/themes/your-theme-name` directory, configure the theme `style.css` file headers and set it as the default theme for your application.

Theme structure
---------------

The theme should be responsible to only handle the views (HTML), its assets like JavaScript and stylesheets files as well as basic templating logic. Here is a look of the default theme structure:

```php
+-- assets/
|    +-- js/
|    +-- sass/
+-- config/
+-- dist/
|    +-- css/
|    +-- images/
|    +-- js/
+-- inc/
+-- languages/
+-- resources/
|    +-- Providers/
+-- views/
+-- functions.php
+-- index.php
+-- package.json
+-- routes.php
+-- style.css
+-- webpack.mix.js
```

### The assets directory

The `assets` directory contains all your JavaScript and CSS files. There is a default `js/theme.js` file as well as default [Sass](https://sass-lang.com/) files extracted from the [Underscore](https://underscores.me/) theme.

### The config directory

The `config` directory contains the theme configuration files. From this folder, you can control your theme images, menus, sidebars, support features and templates. There is also a `theme.php` configuration file that handle theme core configuration like the autoloading PSR-4 parameters, the views paths and the list of its service providers. Each configuration file contains comments with explanations about each available option.

### The dist directory

The `dist` directory contains your compiled assets. Compiled assets are then enqueued by the [Asset]({{url}}/asset) API. Asset compilation is managed by [webpack](https://webpack.js.org/) through [Laravel Mix](https://laravel-mix.com/) tool. You can read more about managing the assets [below](#managing-assets).

### The inc directory

The `inc` directory is a folder that extends the default `functions.php` file. You can insert as many PHP files as you want into the folder and all files will be automatically included. You can event nest PHP files within subfolders.

### The languages directory

The `languages` directory contains the PO template file for your theme. You can load it with the [PoEdit](https://poedit.net/) software and start translating your theme.

### The resources directory

The `resources` directory is the theme namespaced directory. You can organize your theme PHP Class into the folder by following the [PSR-4](https://www.php-fig.org/psr/psr-4/) specification. The `\Theme` namespace is pointing to the `resources` directory. By default, it contains a `Providers` directory that holds the theme `RouteServiceProvider` class.

### The views directory

The `views` directory contains all your view files. By default, it contains views extracted from the [Underscore](https://underscores.me/) theme. Feel free to use, modify or delete default views based on your project.

### Theme routes

The theme provides a `routes.php` file at its root. However, we do not recommend its use. It is now best to define your application routes at project root within the `routes/web.php` file.

Managing assets
--------------

In order to manage your assets, the theme is now bundled with [Laravel Mix](https://laravel-mix.com/). Mix is an elegant wrapper around [Webpack](https://webpack.js.org/) for the 80% use case.

Laravel Mix is the only front-end dependency defined in the theme `package.json` file. With Laravel Mix, you'll be able to compile your JavaScript files as well as your stylesheets and any other required assets for your theme.

### Install Laravel Mix

Before installing Laravel Mix, you need to make sure to have the following tool installed on your machine:

- [NodeJs](https://nodejs.org/en/)

You can handle your node packages using the default `npm` handler provided by NodeJs or you can also install [Yarn](https://yarnpkg.com/en/) for managing them.

Once NodeJs is installed, open a terminal or console and go to your theme root and run the following command:

```bash
npm install
```

or if you're using Yarn:

```bash
yarn install
```

The above command will install Laravel Mix and all its dependencies.

### Configure assets

In order to configure Laravel Mix, you have to edit the `webpack.mix.js` file located at theme root. Here is the default configuration provided by the theme:

```javascript
let mix = require('laravel-mix');

mix.js('assets/js/theme.js', 'dist/js/theme.min.js')
    .sass('assets/sass/style.scss', 'dist/css/theme.css')
    .sass('assets/sass/woocommerce.scss', 'dist/css');

```

Webpack is compiling the `assets/js/theme.js` JavaScript file using the `mix.js()` method and is also compiling the `assets/sass/style.scss` and `assets/sass/woocommerce.scss` Sass files provided by the theme using the `sass()` method.

Laravel Mix provide multiple methods to compile ReactJs components, VueJs components, Less, Stylus, and many more as well as working with BrowserSync, ...

For more configuration options, please read the [official documentation](https://laravel-mix.com/).

### Compile assets

The theme `package.json` file also provides a bunch of scripts/commands in order to help you work with your assets.

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

When enqueueing assets, it is common to provide a version number in order to provide a browser cache hint. The version number of the default theme assets is set based on the theme `Version` header defined into the `style.css` file.

In order to reset browser cache for your assets after a modification, simply update the `Version` header value from the theme `style.css` file.

```css
/*
Theme Name: Themosis
...
Version: 1.0.1
...
Text Domain: themosis
*/
```

Theme assets are loaded from the `AssetServiceProvider` class stored in the `resources/Providers` directory. If you need to remove or add an asset, simply edit the file:

```php
public function register()
    {
        /** @var ThemeManager $theme */
        $theme = $this->app->make('wp.theme');

        Asset::add('theme_styles', 'css/theme.css', [], $theme->getHeader('version'))->to('front');
        Asset::add('theme_woo', 'css/woocommerce.css', ['theme_styles'], $theme->getHeader('version'))->to('front');
        Asset::add('theme_js', 'js/theme.min.js', [], $theme->getHeader('version'))->to('front');
    }
```

> The theme is manage by a `ThemeManager` class. See how the theme `version` value is retrieved from the `style.css` header.

Theme manager
-------------

The framework is handling a theme through a `ThemeManager` class. The manager is responsible to provide utilities in order to work with the current active theme as well as registering it theme configuration.

You can find below a list of methods available in order to work with your theme.

### Get the theme manager

If you need to work inside the theme `functions.php` file, the `ThemeManager` class instance is already provided to you through the `$theme` variable.

The `ThemeManager` class instance is also registered into the application service container under the abstract name `wp.theme`. Here is an example on how to retrieve it from a theme service provider:

```php
<?php
namespace \Theme\Providers;

use Illuminate\Support\ServiceProvider;

class MyServiceProvider extends ServiceProvider
{
    public function register()
    {
        $theme = $this->app->make('wp.theme');
    }
}
```

It is also possible to retrieve the theme manager instance by using the `app()` helper function:

```php
$theme = app('wp.theme');
```

### Get a theme header

Sometimes, you may need to retrieve information from the theme headers defined inside the `style.css` file. In order to get a header value, use the `getHeader` method like so:

```php
$theme = app('wp.theme');
$version = $theme->getHeader('version');
```

Here is the list of available headers you can retrieve from the `style.css` file:

```php
$name = $theme->getHeader('name');
$theme_uri = $theme->getHeader('theme_uri');
$author = $theme->getHeader('author');
$author_uri = $theme->getHeader('author_uri');
$description = $theme->getHeader('description');
$version = $theme->getHeader('version');
$license = $theme->getHeader('license');
$license_uri = $theme->getHeader('license_uri');
$text_domain = $theme->getHeader('text_domain');
```

### Get theme path

If you need to get the theme path or a path of a sub-directory of your theme, you can use the `getPath` method like so:

```php
$theme_root_path = $theme->getPath();

$subdirectory = $theme->getPath('inc/features');
```

### Get theme URL

If you need to get the theme URL or a URL of a sub-directory of your theme, you can use the `getUrl` method like so:

```php
$theme_url = $theme->getUrl();

$theme_subdir_url = $theme->getUrl('dist/images/logo.png');
```

Translations
------------

The theme is translation ready and provides a `languages` directory with a `.po` template file. During development, use the `THEME_TD` constant, defined by the theme manager, as a text domain value when calling gettext functions.

The constant `THEME_TD` value is automatically defined based on theme name during installation but you can easily change it by updating the `Text Domain` header inside the `style.css` file.

Finally you may use the `THEME_TD` constant in your views like so:

```php
// From a Blade view
<button>{{ __("Read more", THEME_TD) }}</button>

// From a Twig view
<button>{{ __("Read more", constant('THEME_TD')) }}</button>
```

WordPress themes only need `.po` files with a locale filename. For example: `en_US`, `fr_BE`,...

Based on defined locale from WordPress, the theme is automatically loading the corresponding translation file for you.

> We recommend the use of [PoEdit](https://poedit.net/) in order to update and translate your files. Check the [Twig]({{url}}/twig) or [Blade]({{url}}/blade) guides in order to configure PoEdit for your views.