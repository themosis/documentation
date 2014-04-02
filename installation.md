Installation
============

1. Requirements
- Install Composer
- Themosis framework suite
- Install Themosis framework
- Activate the framework

1. Requirements:
----------------
The framework has a few system requirements:

- PHP >= 5.3

Otherwise the setup is the same as WordPress. [Check here](http://wordpress.org/about/requirements/) for other WordPress server requirements.

> Please note this framework has only been tested on **Apache** server only.

2. Install Composer
-------------------
Themosis uses [Composer](https://getcomposer.org/) to manage its dependencies and easily load its classes. Follow the instructions [here](https://getcomposer.org/doc/00-intro.md) in order to install Composer either locally or globally on your computer.

On Windows, you can use the Composer [Windows installer](https://getcomposer.org/Composer-Setup.exe).

3. Themosis framework suite
---------------------------
When you download Themosis, you get a "suite" of tools. This set contains 1 plugin and 1 theme.

The plugin:

- **themosis**: Contains the core classes. You will write your code inside the plugin in order to customize your WordPress administration and define the datas of your website or application.

The theme:

- **themosis-theme**: Define the look and feel of your project. Is considered as the "public" part of your website or application. You will define your routes, controllers, assets, views, ... inside this theme.

> Note: The `themosis-theme` folder name can be change to anything you want in order to fit your project. It handles the public/front-end of your website/application.

4. Install Themosis framework
-----------------------------

1. Copy the plugin `themosis` to your `wp-content/plugins` directory.
2. Copy the theme `themosis-theme` to your `wp-content/themes` directory. **Note:** You can rename the folder to fit your project.
3. Open your _Terminal_ or _Console_ and navigate to the `themosis` plugin. There is a `composer.json` ready for installing dependencies.
4. In the _Terminal_ or _Console_, type the following command: `composer update`

This will load the default dependencies used by the framework.

> If you want to install other dependencies or libraries, check the [packagist website](https://packagist.org/).

> Always install the dependencies before trying to activate the `themosis` plugin.

5. Activate the framework
-------------------------

1. Simply connect to your WordPress administration. Go to the `Plugins` page and activate the `themosis` plugin.
2. Finally, under the `Appearance>Themes` page, activate your `themosis-theme`.
3. Visit the public home page and you should see a congratulation message prompting that everything is installed correctly.

Next
----
Check the [configuration guide](https://github.com/themosis/documentation/blob/master/configuration.md)

