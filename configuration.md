Configuration
=============
_Beside the `composer.json` file, there is nothing else to edit in the `themosis-core` plugin._

You'll spend most of your time writing your code inside the `themosis-datas` plugin and `themosis-theme` theme.

1. Folder structure
2. Introduction
3. Configurations

1. Folder structure
-------------------
On both the `themosis-datas` plugin and `themosis-theme` theme, there is a folder called `app`. This is the main folder where you write your code.

The **plugin** folder structure:

- **/admin**: This folder is an extension of the classic `functions.php` file. You can add as many files as you want. **Note:** the framework will load them in alphabetical order. As the name suggests, this is where you'll write your code in order to shape the WordPress administration and setup your datas: custom post types, metaboxes, custom fields, options, ...
- **/config**: Contains the main configuration files: `application`, `constants`, `errors`
- **/models**: Store your model classes.
- **/widgets**: Contains your widget classes.

The **theme** folder structure:

- **/assets**: Insert all your stylesheets, javascripts, images and other public assets inside this folder.
- **/config**: Contains the theme configuration files: `menus`, `sidebars`, `supports`, `templates`.
- **/controllers**: This folder stores all your controllers.
- **/views**: This folder allows you to organize your views - HTML. You can point to subdirectories using a dot syntax.
- **routes.php**:



***
Each `app` folder contains a `config` directory where you can edit and set your website/application configuration: `app/config`


**Plugin configuration:**

* Application
* Constants
* Errors

**Theme configuration:**

1. Menus
2. Sidebars
3. Supports
4. Templates