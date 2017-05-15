Theme development
=================

**Work in progress** guide.

- [Introduction](#introduction)
- [Structure](#structure)
- [Namespace](#namespace)
	- [Namespace management and autoloading](#namespace-management-and-autoloading)
- [Command line tools](#command-line-tools)
- [Translations](#translations)

Introduction
------------

When you start a new Themosis framework project, Composer also installs the `themosis-theme` theme boilerplate inside the `htdocs/content/themes` directory.

The theme is already setup to work with the Themosis framework core and brings configuration for autoloading theme classes, configuration files, service providers, routing and more.

Since release 1.3.0, the theme also bundles GulpJS, WebPack (1.x), BrowserSync and Bower in order to help you work with your theme assets (css, js).

> Do not forget to rename your theme folder and remove the dependency of the `themosis-theme` in your root project `composer.json` file.

Structure
---------

The `themosis-theme` theme structure, since release 1.3.0, is slightly different than previous versions. Here is the new theme file structure:

```php
+-- assets/
|   +-- js/
|   +-- sass/
|   +-- stylus/
+-- dist/
|   +-- css/
|   +-- images/
|   +-- js/
+-- languages/
+-- resources/
|   +-- admin/
|   +-- config/
|   +-- controllers/
|   +-- models/
|   +-- providers/
|   +-- views/
|   +-- widgets/
|   +-- routes.php
+-- functions.php
+-- gulpfile.js
+-- index.php
+-- package.json
+-- style.css
+-- webpack.config.js
```

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
- [Autoprefixr](https://github.com/postcss/autoprefixer)

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

### Gulp commands

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
