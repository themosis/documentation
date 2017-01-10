Upgrade
=======

- [Upgrade from 1.2.* to 1.3.0](#upgrade-from-12-to-130)
- [Upgrade from 1.2.0 to 1.2.3](#upgrade-from-120-to-123)
- [Upgrade from 1.1.* to 1.2.0](#upgrade-from-11-to-120)

These notes cover the steps to follow in order to upgrade major versions of the framework.

Upgrade from 1.2.* to 1.3.0
---------------------------

Follow the steps below if you're using version 1.2.x and want to upgrade to version 1.3.0. Version `1.3.0` has many code breaking changes, so take extra care to each step. We recommend you to upgrade your project using another branch and merge it once complete. For this update, you'll need to upgrade the root files, your custom theme root files and code and finally run a tiny database query in order to make your existing project work with the Themosis framework `1.3.0`.

### Upgrade project root

1. Rename the `bootstrap/start.php` file into `bootstrap/autoload.php`.
2. Replace the `bootstrap/autoload.php` file content with [this one](https://raw.githubusercontent.com/themosis/themosis/master/bootstrap/autoload.php).
3. Replace the `config/shared.php` content with [this file](https://raw.githubusercontent.com/themosis/themosis/master/config/shared.php).
4. Update your `config/environments/local.php` file accordingly based on the [new one](https://raw.githubusercontent.com/themosis/themosis/master/config/environments/local.php).
5. Remove deprecated environments constants from all of your environments files (local, staging, production, ...):
    - `THEMOSIS_ERROR_DISPLAY`
    - `THEMOSIS_ERROR_SHUTDOWN`
    - `THEMOSIS_ERROR_REPORT`
6. If you use any of these deprecated constants, be sure to update your project accordingly.
7. Replace `htdocs/wp-config.php` file with [this one](https://raw.githubusercontent.com/themosis/themosis/master/htdocs/wp-config.php).
8. Replace `library/Thms/Config/Environment.php` class content with this [updated class](https://raw.githubusercontent.com/themosis/themosis/master/library/Thms/Config/Environment.php).
9. Add a `twig` sub-folder into the `storage` directory.
10. Remove the `.php` extension to all of your `.env.{location}` files.
11. Update all `.env.{location}` files content based on the [DotEnv package](https://github.com/vlucas/phpdotenv) syntax. [Here is an example](https://raw.githubusercontent.com/themosis/themosis/master/.env.local) of the default `.env.local` file to help you update your code.
12. Update the root `composer.json` file based on the framework [new one](https://raw.githubusercontent.com/themosis/themosis/master/composer.json) and update your `require` statements based on your project needs.
13. Before running an update, verify that your current developed theme folder is not named `themosis-theme` otherwise it will be overwritten. In some cases, you must also delete your `composer.lock` and `themosis-framework` folder in order to get latest and overwrite cached versions.
14. Open your command line or console and update your composer installation by running first a `composer self-update` then update your project dependencies by running `composer update`. This will install all new framework dependencies.

### Upgrade theme

If you have followed the previous steps, the framework core is up-to-date and a copy of the new `themosis-theme` has been installed as well into your themes directory for reference. The following steps will guide through the files to update, remove and move in order to make your custom theme compatible.

1. Remove the `bootstrap` folder.
2. From the new `themosis-theme`, copy its `functions.php` file and replace your theme’s `functions.php` file.
3. Remove all the code from your theme `index.php` file. Leaving the file empty.
4. Remove all theme `index.php` files placeholders except the `index.php` file located at theme’s root.
5. Add a `languages` folder in your theme root in order to load your translation files if any.
6. Move the `assets` folder located inside the `resources` directory to your theme’s root.
7. At theme's root, create a `dist` folder and add `css`, `images` and `js` folders into it.
8. If you don't have one already, copy the `package.json` file from the `themosis-theme` into yours.
9. If you don't already use [Gulp](http://gulpjs.com/), copy the `gulpfile.js` file from the `themosis-theme` into yours.
10. If you don't already have a `webpack.config.js` file, copy the `webpack.config.js` file from the `themosis-theme` into yours.
11. _(optional)_ New theme is bundled with Gulp and Webpack to handle assets. If you have existing Gulpfile, Bower,… configuration files, it’s optional to update them but Gulp is configured to look for assets inside the `assets` folder and compile them into the `dist` directory.
12. _(optional)_ Move your previously compiled CSS files into the new `dist/css` directory.
13. _(optional)_ Move your previously compiled JS files into the new `dist/js` directory.
14. Find and replace all references of the `themosis_assets()` function with the `themosis_theme_assets()` function.
15. Rename the `application.config.php` file to `theme.config.php`.
16. Update the `theme.config.php` alias property with [new one](https://raw.githubusercontent.com/themosis/theme/master/resources/config/theme.config.php). Do not forget to add your custom aliases if previously defined.
17. Copy the `themosis-theme` `providers.config.php` file inside your theme’s `resources/config` directory.
18. Copy the `themosis-theme` `resources/providers` directory and its content into your theme's `resources` folder.
19. Update the `loading.config.php` file and replace all instances of the previous path `themosis_path(‘theme’)` by `themosis_path(‘theme.resources’)`
20. The new theme use a default PHP namespace, `Theme\\`. Append the service providers auto-loading rule into your `loading.config.php` file: `'Theme\\Providers\\' => themosis_path('theme.resources').'providers'`.
21. Update your models loading namespace key to `Theme\\Models\\`.
22. Update your controllers loading namespace key to `Theme\\Controllers\\`.
23. Update all your controllers and models classes to use the new theme namespace. For example on controllers classes: `namespace Theme\Controllers;`. If you already use custom namespaces for your theme, keep it and update files accordingly.
24. _(optional)_ If you already use custom namespace for your controllers, you have to update the `resources/providers/RoutingService` namespace in order to correctly use your controllers within the routes. Find and replace the `'Theme\Controllers'` by your into the Route group method namespace property.
25. Inside your `resources/routes.php` file, update your controller classes references to only their class name without the namespace. For example, if you had a class controller full name of `Company\Awesome\Controller\Pages@show`, such parameter would be now `Pages@show`.
26. The `BaseController` class is no longer bundled within the theme. Please define its path by adding the following use statement at the top of each of your controller classes: `use Themosis\Route\BaseController;`.
27. Find and replace all instances of the `THEMOSIS_TEXTDOMAIN` constant by `THEMOSIS_THEME_TEXTDOMAIN` constant. Make sure to check your config files as well.
28. The `Session::nonceName` and `Session::nonceAction` constants have been removed. If you used them into your code, please define your own nonces.
29. The PostType `getSlug()` method is deprecated. Update your code and use the `get(‘name’)` method.
30. The Form `checkboxes()` method is deprecated. Update your code to use the `checkbox()` method instead.
31. Rename all your view files to now use the `.blade.php` extension in place of the deprecated `.scout.php` extension.
32. The directive `{{ }}` prints and escapes values now and the `{!! !!}` directive only prints the value. Update your views accordingly to avoid HTML content being escaped like the post content for example.
33. For those using the Themosis page templates, the current version is now using WordPress core templates. Meaning that all the code referencing the meta key `_themosisPageTemplate` must be updated and reference the meta key `_wp_page_template`. Once your code updated, please refer to the last step below in order to update your `postmeta` database table.

> For users who were working on the master branch, please update all references of `container('config')` or `$theme['config']` by `container('config.factory')` and `$theme['config.factory']` accordingly from your theme `functions.php` file.

### Upgrade database

Since the framework now uses the WordPress core template system. You need to update your database `postmeta` table in order for your code to find the right page template. Open a MySQL console and run the following query (please change table name based on your database prefix):

1. `UPDATE wp_postmeta SET meta_key = '_wp_page_template' WHERE meta_key = '_themosisPageTemplate';`
2. Done! Congratulations.

Upgrade from 1.2.0 to 1.2.3
---------------------------

Follow the steps below if you're using version 1.2.x and want to upgrade to version 1.2.3.

**Note:**

Habitually minor release doesn't need a manual process but this one is an exception in order to fix the PHP class alias issue. Since release 1.2.0, class aliases have been moved from the theme to the core plugin. The idea behind this was to provide core aliases to the theme but as well to custom plugins.

But we have an issue. Defining class aliases into plugin is prone to conflicts between plugins and the currently active theme. After testing some ideas to avoid name conflicts, it was clear that plugins won't be able to define class aliases or put in other words, won't be allowed to define class aliases using the PHP class_alias function. Developers will have to use PHP namespace and its features for the development of their plugins.

Please note that plugin development is still in its infancy with the framework. Plugin development with full documentation will be available for release 1.3.0. only.

In the meantime, update your project and follow the steps below to get version 1.2.3:

1. Be sure your working theme has been renamed to something else than `themosis-theme`.
2. Open the Console or Terminal and run `composer update` from your project root.
3. Replace your theme's `bootstrap/start.php` file by [this one](https://github.com/themosis/theme/blob/1.2.3/bootstrap/start.php).
4. Open your theme's `config/application.config.php` file and update the `aliases` property with the array list from [this gist](https://gist.github.com/jlambe/11abeed1f6082a44b810#file-application-config-php).
5. Upgrade complete.

Upgrade from 1.1.* to 1.2.0
---------------------------

Follow the steps below if you're using version 1.1.x and want to upgrade to version 1.2.0.

1. Create a `storage` folder at project root (same location as your main composer.json file).
2. Inside the `storage` folder, create a `views` directory.
3. Update your `composer.json` file and set your `themosis-framework` and `themosis-theme` version to `1.2.*`.
4. Run `composer update`.
5. Update the `start.php` file located inside the `bootstrap` folder at project root with [this file](https://github.com/themosis/themosis/blob/master/bootstrap/start.php).
6. Next, in your theme, replace `functions.php` file by the one found in the new installed *themosis-theme*.
7. Rename your theme `app` folder to `resources`.
8. Copy the `bootstrap` folder from the new installed *themosis-theme* into your own theme.
9. Update your `loading.config.php` file by the one found in the new installed *themosis-theme*. PSR-4 autoloading is used in this version. Update your custom classes autoloading statements accordingly.
10. Aliases defined inside `application.config.php` are now by default defined inside the core framework at least for core classes. You can override their aliases or add yours for your custom classes. If you don't have any custom aliases, you can specify an empty array by default.
11. The Field API has been rewritten. If you have `select` and `editor` fields, check the new [field documentation]({{url}}/field) and update those fields as their arguments have been changed.
12. Delete the installed `themosis-theme`.
13. Upgrade complete. Enjoy!

Check the [change log]({{url}}/changelog) page for a list of new features and fixes available. Also, the documentation has been completely rewritten for this release with more examples, make sure to check it out.

> If you have an error with your views storage, your project is missing the constant `THEMOSIS_STORAGE`. Please check step 5 above to fix this.

