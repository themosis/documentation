Upgrade
=======

- [Upgrade from 1.2.0 to 1.2.3](#upgrade-from-120-to-123)
- [Upgrade from 1.1.* to 1.2.0](#upgrade-from-11-to-120)

These notes cover the steps to follow in order to upgrade major versions of the framework.

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

