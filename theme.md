Theme development
=================

> In progress...

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
- [Compiling assets](#compiling-assets)
- [Namespace](#namespace)
    - [Namespace management and autoloading](#namespace-management-and-autoloading)
- [Command line tools](#command-line-tools)
    - [Installation](#installation)
    - [Use GulpJs](#use-gulpjs)
    - [Gulp Commands](#gulp-commands)
    - [BrowserSync](#browsersync)
    - [Sass](#sass)
    - [Stylus](#stylus)
    - [Autoprefixer](#autoprefixer)
    - [Javascript & WebPack](#javascript-&-webpack)
    - [Bower](#bower)
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

The `dist` directory contains your compiled assets. Compiled assets are then enqueued by the [Asset]({{url}}/asset) API. Asset compilation is managed by [webpack](https://webpack.js.org/) through [Laravel Mix](https://laravel-mix.com/) tool. You can read more about managing the assets [below](#compiling-assets).

### The inc directory

The `inc` directory is a folder that extends the default `functions.php` file. You can insert as many PHP files as you want into the folder and all files will be automatically included. You can event nest PHP files within subfolders.

### The languages directory

The `languages` directory contains the PO template file for your theme. You can load it with the [PoEdit](https://poedit.net/) software and start translating your theme.

### The resources directory

The `resources` directory is the theme namespaced directory. You can organize your theme PHP Class into the folder by following the [PSR-4](https://www.php-fig.org/psr/psr-4/) specification. The `\Theme` namespace is pointing to the `resources` directory. By default, it contains a `Providers` directory that holds the theme `RouteServiceProvider` class.

### The views directory

The `views` directory contains all your view files. By default, it contains views extracted from the [Underscore](https://underscores.me/) theme. Feel free to use, modify or delete default views based on your project.

### Theme routes








When working with the theme assets, all your `sass`, `stylus`, `js` source files should be inside the appropriate `assets` directory. The theme Gulp configuration is set to compile and "minify" all assets into the `dist` directory.

For example, a JS file stored in `assets/js/theme.js` is compiled into the `dist` directory like so `dist/js/theme.min.js`.

So when calling an `Asset::add()` statement, the asset referenced in the method should always be stored into the `dist` directory. Here is an example:

```php
// The following asset is stored into the 'dist/js/theme.min.js'
Asset::add('theme-js', 'js/theme.min.js', ['jquery'], '1.0.0', true);
```

Read the [command line tools](#command-line-tools) section below for details regarding the available tools and their commands.

Namespace
---------

Since release 1.3.0, all classes used in a theme (as well as in a custom plugin) have to use a PHP namespace.

By default, the `themosis-theme` theme comes with a `Theme` base namespace for its classes. Here is the list of the default namespaces inside the theme and to which directory it points to:

```php
+-- Theme\Controllers -> 'resources/controllers'
+-- Theme\Models -> 'resources/models'
+-- Theme\Providers -> 'resources/providers'
```

> Note: the `resources` could have been map to the default `Theme` namespace but it's not. Do no try to put a class at the root of the `resources` directory as it won't be autoloaded.

### Namespace management and autoloading

In order to control your theme namespace, you need to edit the `loading.config.php` file stored into the `resources/config` directory of your theme.

Within the file, you can define as the `key`, your classes namespace and as the `value`, the path to the directory where you store your PHP classes.

Here is the default configuration:

```php
return [
    'Theme\\Controllers\\' => themosis_path('theme.resources').'controllers',
    'Theme\\Models\\' => themosis_path('theme.resources').'models',
    'Theme\\Providers\\' => themosis_path('theme.resources').'providers'
];
```

The theme is using the PSR-4 autoloading. So you can as many directories and namespace definitions to your custom theme.

> If you no longer wish to use the default `Theme` namespace, and for example use your company name as default like `Themosis`, make sure to update all namespace definitions into your theme classes.

In the above example, the `themosis_path()` is global helper function which helps you retrieve any registered path within your theme. Theme paths are defined inside the theme's `functions.php` file.

Command line tools
------------------

The theme is bundled with a `package.json` file and define the following development dependencies:

- [GulpJs](http://gulpjs.com/) (v4)
- [WebPack](https://webpack.github.io/) (1.x)
- [BabelJs](https://babeljs.io/)
- [BrowserSync](https://www.browsersync.io/)
- [Sass](http://sass-lang.com/)
- [Stylus](http://stylus-lang.com/)
- [Autoprefixer](https://github.com/postcss/autoprefixer)

### Installation

Before going further, you need to install the theme dependencies defined inside the `package.json` file.

> In order to work, make sure to have NodeJs installed on your computer as well as its package manager `npm`. Please [read the official documentation](https://nodejs.org/en/) for installing npm.

Once NodeJs is installed, open your Terminal or Console and go to the theme's root directory then type the following command `npm install` and press enter. It will take a moment to install the dependencies into a `node_modules` folder (which you should make sure to add to your .gitignore rules).

### Use GulpJs

Because we use GulpJs 4 (beta) for development, there is a chance that your default global Gulp command won't work. Also as GulpJs is defined as a dependency, all you need to do work with it is to call it locally from your theme's root folder like so:

```js
./node_modules/.bin/gulp -v
```

The previous command should output the CLI Gulp version if installed globally as well as the local version (the one from the theme). If you don't install GulpJs 4.x (beta) globally, make sure to always reference the local version like in the example above.

> For brevity, I'll write the shorthand `gulp` command instead of the local command.

### Gulp Commands

The theme `gulpfile.js` configuration contains 2 commands that are available to developers from the command-line:

- `gulp watch`
- `gulp build`

#### Watch

The watch command is the development command. It continuessly look after any changes done to one of your `sass`, `stylus` or `js` files stored inside the `assets` directory.

Every time you change one of your asset, gulp compiles and minifies the asset to its proper `dist` folder, provides source maps and refreshes your active browser window thanks to BrowserSync.

In order to start working, simply run the `gulp watch` command from the theme's root directory.

#### Build

The build command is the production command. It compiles, compresses and minifies your `sass` or `stylus` files as well as your `js` files and stores them into the same `dist` directory as when using the watch command so your `Asset` calls do not trigger errors.

### BrowserSync

[BrowserSync](https://www.browsersync.io/) saves you time by handling live-reloading and synchronizing your browsers during development and testing. By default, the BrowserSync feature is only active on a `local` environment.

In order to start working with BrowserSync, simply run the `gulp watch` command from your theme's root directory. Once the command is started, you should see a message like the following in your Terminal or Console:

```bash
[15:27:30] Starting 'watch'...
[BS] Copy the following snippet into your website, just before the closing </body> tag
<script id="__bs_script__">//<![CDATA[
    document.write("<script async src='https://HOST:3000/browser-sync/browser-sync-client.js?v=2.18.9'><\/script>".replace("HOST", location.hostname));
//]]></script>

[BS] Access URLs:
-------------------------------------
         UI: http://localhost:3001
-------------------------------------
UI External: http://192.168.1.33:3001
-------------------------------------
```

Like the above message explains, you have to copy/paste the following code snippet right before the closing body tag `</body>`:

```html
<script id="__bs_script__">//<![CDATA[
    document.write("<script async src='https://HOST:3000/browser-sync/browser-sync-client.js?v=2.18.9'><\/script>".replace("HOST", location.hostname));
//]]></script>
```

Please note the version number right after the `v` query string variable from the snippet above. In this example, the version number is `2.18.9`. Now, from your text editor or IDE, please open the `resources/admin/actions.php` stored in your theme. Look after the code called from the `wp_footer` action hook:

```php
Action::add('wp_footer', function()
{
    if (defined('BS') && BS):
        ?>
        <script type="text/javascript" id="__bs_script__">
            //<![CDATA[
            document.write('<script async src="http://HOST:3000/browser-sync/browser-sync-client.2.12.7.js"><\/script>'.replace("HOST", location.hostname));
            //]]>
        </script>
        <?php
    endif;
});
```

Within the code block, look after the version `v` query string and if the default theme's code version number is not the same as the one from your Terminal or Console, please update it, save your file and reload the browser.

> BrowserSync is currently only ran in the front-end part of your application. If your assets are for the WordPress administration, change the action hook to `admin_footer`

#### Toggle BrowserSync On/Off

If you have installed the Themosis full stack framework, there is `BS` constant parameter defined into each environment file that controls whether or not the theme should output the BrowserSync script:

```php
// Stored in the config/environments/local.php
define('BS', true);
```

By default, it is set to `true` for the local environment. Set it to `false` to toggle off BrowserSync.

### Sass

The `guplfile.js` file has tasks defined in order to compile `.scss` files stored inside the theme's assets folder `assets/sass`.

By default, a `screen.scss` file is located at the root of the sass directory. It is the main sass file. This file will be compiled as `screen.min.css` into your `dist/css` folder.

> **Important:** In fact, all sass files stored at the root of the sass directory are compiled to their respective `filename.min.css` file inside the `dist/css` folder.

We highly encourage you to structure your sass files within sub-directories of the `assets/sass` folder and to import them into the main `screen.scss` file. This allows you to create many CSS files depending of their use (for administration for example) and import only the shared or specific styles per main file.

> During development, using the `watch` command, your assets are compiled with sourcemaps for debugging. When releasing your theme, always make sure to run the `build` command in order to fully minified and compressed your CSS file.

#### Bootstrap CSS

The sass tasks are also compatible and set to work with the [Bootstrap CSS](https://www.getbootstrap.com) framework in its sass version.

### Stylus

For those who like to save some typos and enjoy working with [Stylus](http://stylus-lang.com/), we got you covered.

Just like for Sass, by default, a `screen.styl` file is defined at the root of the `assets/stylus` folder. All files located at the root of the `assets/stylus` folder are compiled to the `dist/css` directory.

> If for some reasons you work with both sass ans stylus files, make sure that their main files do not have the same filename as at each compilation one is overwriting the other.

### Autoprefixer

All your compiled Sass or Stylus files are parsed through the [Autoprefixer](https://github.com/postcss/autoprefixer) library so you don't need to care about browsers prefixes in your CSS.

### Javascript & WebPack

Javascript files are handled by [WebPack 1.x](https://webpack.github.io/). At the root of the `themosis-theme`, you'll find a `webpack.config.js` file in order to manage WebPack compilation.

> The theme is working with WebPack version 1.x.

Within the `webpack.config.js` file, you'll find one entry point by default:

```js
entry: {
    theme: path.join(__dirname, 'assets/js/theme.js')
},
```

By default, the theme is looking after the `assets/js/theme.js` file and compiles it to the `dist/js/theme.min.js` file. The `assets/js/theme.js` file is your theme's main JS file. Both `watch` and `build` commands handle ES2015 and beyond javascript thanks to the use of the [BabelJs](http://babeljs.io/) compiler that compiles your modern code to its respective Ecmascript 5 version.

#### Define multiple JS files

Like explained above, the `webpack.config.js` file, by default, only contains one entry point which is the `assets/js/theme.js` file. Adding Js files to the root of the `assets/js` directory won't automatically compiled them.

In order to define another main JS file, you need to add an entry point to the `webpack.config.js` file. For example, let's add a `assets/js/superfeature.js` file that compiles to a `dist/js/admin.js` file by modifying the `entry` property of our configuration file:

```php
entry: {
    theme: path.join(__dirname, 'assets/js/theme.js'),
    admin: path.join(__dirname, 'assets/js/superfeature.js')
},
```

> If a `watch` command is already running when you edit the `webpack.config.js` file, you need to restart your `watch` command in order for WebPack to take your changes into consideration.

Now, each time you modify any JS files, or build your scripts for production, you'll find a `dist/js/theme.min.js` and `dist/js/admin.min.js` files.

### Bower

If you have [Bower](https://bower.io/) installed on your machine, the theme contains a `.bowerrc` configuration file as well as a `bower.json` file for storing dependencies.

When installing dependencies (components) through Bower, they will be stored into the `assets/components` directory.

Translations
------------

The `themosis-theme` theme is translation ready.

At the root of the theme, a `languages` directory is defined with sampled `.po` files. During theme development, use the `THEME_TEXTDOMAIN` constant as a textdomain value for the gettext functions. For example:

```php
// From a Blade view
<button>{{ __("Read more", THEME_TEXTDOMAIN) }}</button>

// From a Twig view
<button>{{ __("Read more", constant('THEME_TEXTDOMAIN')) }}</button>
```

WordPress themes only need `.po` files with a locale filename. For example: `en_US`, `fr_BE`,...

Based on defined locale from WordPress, the theme is automatically loading the corresponding translation file for you.

> We recommend the use of [PoEdit](https://poedit.net/) in order to update and translate your files. Check the [Twig]({{url}}/twig) or [Blade]({{url}}/blade) guides in order to configure PoEdit for your views.