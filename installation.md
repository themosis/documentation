Installation
============

- [Requirements](#requirements)
- [Install Composer](#install-composer)
- [Install the Themosis framework](#install-the-themosis-framework)
    - [The public directory](#the-public-directory)
- [Install the theme](#install-the-theme)
- [Laravel Valet](#laravel-valet)
- [Laravel Homestead](#laravel-homestead)
- [Artisan](#artisan)
- [Install the theme](#install-the-theme)
- [Install WordPress](#install-wordpress)

Requirements:
-------------

The server requirements are basically the same as for [WordPress](https://wordpress.org/about/requirements/) with the addition of a few ones:

- Composer >= 2.0
- PHP >= 8.0
- [Intl PHP Extension](http://php.net/manual/fr/intro.intl.php)

Install Composer
----------------

The Themosis framework uses [Composer](https://getcomposer.org/) to manage its dependencies and easily load its files. Please follow the instructions [here](https://getcomposer.org/doc/00-intro.md) in order to install Composer either locally or globally on your computer.

On Windows, you can use the Composer [Windows installer](https://getcomposer.org/Composer-Setup.exe).

> We recommend you to install Composer globally on your system.

Install the Themosis framework
------------------------------

Open your `Terminal` or `Console` and execute the following command:

```bash
composer create-project themosis/themosis my-project-name
```

This will create a directory called `my-project-name` on your computer and automatically download the latest WordPress version along with the latest Themosis framework version and its dependencies.

### The public directory

The Themosis framework is not configured to be hosted like a classic WordPress project. Compared to a classic WordPress installation, a Themosis project root is not the web server public directory.

When configuring your web server, make sure to point the web server to the project **htdocs** directory.

Laravel Valet
-------------

By default, the Themosis framework 3.0 supports [Laravel Valet](https://laravel.com/docs/valet) and is packaged with a local driver.

> Currently, the local driver only supports a WordPress single instance installation. If you need to develop a multisite WordPress application, we recommend you to use [Laravel Homestead](#laravel-homestead).

Laravel Homestead
-----------------

The Themosis framework also works with [Laravel Homestead](https://laravel.com/docs/homestead).

Laravel Homestead can be installed [globally](https://laravel.com/docs/homestead#installation-and-setup) or on a [per-project](https://laravel.com/docs/homestead#per-project-installation) basis.

> Since release 3.0, the Laravel Homestead package is no longer installed by default with the Themosis framework.

In order to use Laravel Homestead on a per-project basis, make sure [all requirements](https://laravel.com/docs/homestead#first-steps) are installed on your computer then add the `laravel/homestead` package to your project like so:

```bash
composer require laravel/homestead --dev
```

Once the package is installed, from your project root, run the following command in order to generate the required configuration files:

```bash
# macOS / Linux...
php vendor/bin/homestead make
 
# Windows...
vendor\\bin\\homestead make
```

The code will install, at project root, a group of files in order to bootstrap your virtual machine. Open the `Homestead.yaml` file and change the `sites -> to` value to point to the framework default public directory `htdocs`. Here is an example of a default configuration:

```yaml
ip: 192.168.10.10
memory: 2048
cpus: 1
provider: virtualbox
authorize: ~/.ssh/id_rsa.pub
keys:
    - ~/.ssh/id_rsa
folders:
    -
        map: /Users/username/web/my-project-name
        to: /home/vagrant/code
sites:
    -
        map: homestead.test
        to: /home/vagrant/code/htdocs
databases:
    - homestead
name: themosis
hostname: themosis
```

> For more information regarding Homestead configuration, please read [the official documentation](https://laravel.com/docs/homestead).

Once your configuration of Homestead is complete, from the Terminal or Console, run the following command in order to start your development environment:

```bash
vagrant up
```

Before visiting the browser and in order to install WordPress, please make sure to first read the [configuration guide]({{url}}/configuration).

Artisan
-------

With release 3.0 of the Themosis framework, we have replaced our previous Console CLI tool by the Laravel Artisan CLI tool. All commands from our previous tool are available with Artisan.

In order to view a list of all available commands, from your project root, run the following command:

```shell
php artisan
```

For more information regarding Artisan, please read the [official documentation](https://laravel.com/docs/artisan).

Install the theme
-----------------

On project installation, there is no default theme installed.

In order to install the new theme, go to the project root and run the `theme:install` command from the terminal:

```bash
php artisan theme:install my-theme
```

The command will download and unzip a new theme into the `htdocs/content/themes/my-theme` directory. The script is also setting up the theme `style.css` file headers, the theme text domain and set it as the default theme for your WordPress application.

Install WordPress
-----------------

Habitually, you define your environment variables like database name, database user, authentication salts and other WordPress configurations inside the `wp-config.php` file.

With the Themosis framework, those configurations are extracted from the web root and located in the parent folder, at project root through the use of a `.env` file.

Follow the [configuration guide]({{url}}/configuration) and see how to configure your WordPress application.

> Please note that the environment `APP_DEBUG` variable is now set to `false` by default in your `.env` file. We have turned off this setting due to WordPress still not being fully compatible with PHP8.1 and above. Feel free to turn on debug mode once WordPress is installed during development. 

Next
----
Read the [configuration guide]({{url}}/configuration)