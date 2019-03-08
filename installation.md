Installation
============

- [Requirements](#requirements)
- [Install Composer](#install-composer)
- [Install the Themosis framework](#install-the-themosis-framework)
- [Install the theme](#install-the-theme)
- [Laravel Homestead](#laravel-homestead)
- [Install WordPress](#install-wordpress)

> Themosis framework 2.0 is in a **Release Candidate** state.

Requirements:
-------------

The server requirements are basically the same as for [WordPress](https://wordpress.org/about/requirements/) with the addition of a few ones:

- PHP >= 7.1
- [Intl PHP Extension](http://php.net/manual/fr/intro.intl.php)

> We recommend to use at least PHP 7.2+

Install Composer
----------------

The Themosis framework uses [Composer](https://getcomposer.org/) to manage its dependencies and easily load its files. Please follow the instructions [here](https://getcomposer.org/doc/00-intro.md) in order to install Composer either locally or globally on your computer.

On Windows, you can use the Composer [Windows installer](https://getcomposer.org/Composer-Setup.exe).

> We recommend you to install Composer globally on your system.

Install the Themosis framework
------------------------------

Open your `Terminal` or `Console` and execute the following command:

```bash
composer create-project themosis/themosis my-project-name 2.0-RC1
```

This will create a directory called `my-project-name` on your computer and automatically download the latest WordPress version along with the latest Themosis framework version and its dependencies.

Laravel Homestead
-----------------

By default, the Themosis framework is packaged with [Laravel Homestead](https://laravel.com/docs/5.7/homestead) so you can quickly start a local development environment.

If you want to develop your application with Homestead, you first need to have [Vagrant](https://www.vagrantup.com/) installed on your computer. Once installed, open your Terminal or Console at project root and run the following command:

```bash
cd my-project-name
./vendor/bin/homestead make
```

The code will install at project root a group of files in order to bootstrap your virtual machine. Open the `Homestead.yaml` file and change the `sites -> to` value to point to the framework default public directory `htdocs`. Here is an example of a default configuration:

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

> For more information regarding Homestead configuration, please read [the official documentation](https://laravel.com/docs/5.7/homestead).

Once your configuration of Homestead is complete, from the Terminal or Console, run the following command:

```bash
vagrant up
```

Before visiting the browser in order to install WordPress, please make sure to first read the [configuration guide]({{url}}/configuration).

Install the theme
-----------------

By default, the default theme is no longer installed using Composer.

Instead, a CLI tool is now bundled with the framework and installed at project root. In order to install the new theme, connect into your [local virtual machine](#laravel-homestead), go to the project root and run the `theme:install` command from the terminal:

```bash
php console theme:install my-theme
```

The command will download and unzip a new theme into the `htdocs/content/themes/my-theme` directory. The script is also setting up the theme `style.css` file headers, the theme text domain and set it as the default theme for your WordPress application.

Install WordPress
-----------------

Habitually, you define your environment variables like database name, database user, authentication salts and other WordPress configurations inside the `wp-config.php` file.

With the Themosis framework, those configurations are extracted from the web root and located in the parent folder, at project root through the use of a `.env` file.

Follow the [configuration guide]({{url}}/configuration) and see how to install your WordPress application.

Next
----
Read the [configuration guide]({{url}}/configuration)