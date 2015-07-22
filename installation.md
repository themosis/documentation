Installation
============

1. Requirements
2. Install Composer
3. Install Themosis framework
4. Project structure

1. Requirements:
----------------

The framework has a few system requirements before getting started:

- PHP >= 5.4

Otherwise the server requirements are the same as WordPress. [Check here](http://wordpress.org/about/requirements/) for default WordPress server requirements.

2. Install Composer
-------------------

The Themosis framework uses [Composer](https://getcomposer.org/) to manage its dependencies and easily load its files. Please follow the instructions [here](https://getcomposer.org/doc/00-intro.md) in order to install Composer either locally or globally on your computer.

On Windows, you can use the Composer [Windows installer](https://getcomposer.org/Composer-Setup.exe).

> We recommend to install Composer globally on your system.

3. Install Themosis framework
-----------------------------

Open your `Terminal` or `Console` and execute the following command:

```bash
composer create-project themosis/themosis my-project-name
```

This will create a directory called `my-project-name` on your system and automatically download the latest WordPress version along with the latest Themosis framework version and its dependencies.

4. Project structure
--------------------

Open your `my-project-name` folder. You should find the following files and directories:

- bootstrap/
- config/
	- environments/
	- environment.php
	- shared.php
- htdocs/
	- cms/
	- content/
	- index.php
	- wp-config.php
- library/
- vendor/
- .env.local.php
- .env.production.php
- composer.json
- wp-cli.yml 

The `bootstrap` directory is where the framework paths, Composer autoloading, framework environment, constant declarations, and shared configurations are setup.

The `config` directory is where environments such as `local` and `production` are defined and configured.

The `environments` directory is where your `local` and `production` configuration files belong.

The `htdocs` folder is the web root directory. Because WordPress is defined as a dependency, the framework installs the latest WordPress version inside the `cms` folder.

The framework also modify the default `wp-content` location. Your WordPress plugins and themes should all be installed inside the `content` directory.

> Your project will have the following `vendor` directory installed. This `vendor` directory is created by default by Composer. It stores package dependencies used by the Themosis framework.

The `library` directory contains a custom class that handles the loading of the environment. It's another framework dependency and may later contain other classes to help bootstrap the framework.

> Note: It could also have been written in a separate package and loaded inside the `vendor` directory.

The `vendor` directory is created by Composer and is where package dependencies for the framework is stored.

#### Installation

Normally, you define your environment variables like database name, database user, authentication salts and other WordPress configurations inside the `wp-config.php` file.

With the Themosis framework, those configurations are extracted from the web root and located in the parent folder. The framework allows you to define multiple environments: local, production, staging,... and to specify configurations and access per environment.

> Please dig into the **Environment** guide and see how to install your WordPress application on a local and production environment/server.

Next
----
Read the [environment setup guide](http://framework.themosis.com/docs/environment/)
