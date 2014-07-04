Installation
============

1. Requirements
2. Install Composer
3. Install WP-Cli
4. Download the Themosis framework
5. Install WordPress


1. Requirements:
----------------

The framework has a few system requirements before getting started:

- PHP >= 5.3.3

Otherwise the server requirements are the same as WordPress. [Check here](http://wordpress.org/about/requirements/) for default WordPress server requirements.

> Please note this framework has only been tested on **Apache** server only.

2. Install Composer
-------------------

The Themosis framework uses [Composer](https://getcomposer.org/) to manage its dependencies and easily load its classes. Please follow the instructions [here](https://getcomposer.org/doc/00-intro.md) in order to install Composer either locally or globally on your computer.

On Windows, you can use the Composer [Windows installer](https://getcomposer.org/Composer-Setup.exe).

> We recommend to install Composer globally on your system.

3. Install WP-CLI
-----------------

The Themosis framework uses [WP-CLI](http://wp-cli.org/) to install the latest WordPress version. Beside installing WordPress, [WP-CLI](http://wp-cli.org/) will allow you to perform tasks on your WordPress installation using the command-line tool.

> Please first check the [WP-CLI requirements](http://wp-cli.org/#requirements) before installing it.

### Install WP-CLI on UNIX like environment.

Execute the following statements in your `Terminal`:

```bash
curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
```

```bash
chmod +x wp-cli.phar

sudo mv wp-cli.phar /usr/local/bin/wp
```

This will allow you to use the `wp` command in your Terminal.

### Install WP-CLI on Windows.

Run the following composer statement from your `Console`:

```bash
composer create-project wp-cli/wp-cli --no-dev
```

This will download and create a directory `wp-cli` on your system. We recommend to store this directory on a location like `c:\developer\tools\` to avoid mixing your development tools and personal files.

Then add the `wp-cli\bin` folder to your system variable `PATH`. For example, using the recommended installation location, you'll store the following path to your system variable `PATH`: `c:\developer\tools\wp-cli\bin;`

This will allow you to use the `wp` command in your Console.

4. Download the Themosis framework
---------------------------------

Open your `Terminal` or `Console` and execute the following command:

```bash
composer create-project themosis/themosis my-project-name --prefer-dist
```

This will create a directory called `my-project-name` on your system and automatically download the latest WordPress version along with the latest Themosis framework version and its dependencies.

5. Install WordPress
--------------------

Your project comes with a default `wp-config.php` file configured to load the Themosis framework dependencies. Use this file to configure your database, nonce and common WordPress configuration.

> Your project will have the following `vendor` directory installed beside the default WordPress files. This `vendor` directory is created by default by Composer. It stores package dependencies used by the Themosis framework.

Once your `wp-config.php` file is setup, start the default WordPress installation process and log in your WordPress administration. Activate the Themosis framework plugin and the Themosis framework theme.

Visit your project home page and you should be granted with a welcome message. Congratulations! You have installed WordPress and the Themosis framework

> If you don't see the welcome message, check our troubleshooting information page for help.

Next
----
Read the [configuration guide](https://github.com/themosis/documentation/blob/master/configuration.md)

