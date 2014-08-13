Installation
============

1. Requirements
2. Install Composer
3. Install Themosis framework

1. Requirements:
----------------

The framework has a few system requirements before getting started:

- PHP >= 5.3.3

Otherwise the server requirements are the same as WordPress. [Check here](http://wordpress.org/about/requirements/) for default WordPress server requirements.

> Please note this framework has only been tested on **Apache** server only.

2. Install Composer
-------------------

The Themosis framework uses [Composer](https://getcomposer.org/) to manage its dependencies and easily load its files. Please follow the instructions [here](https://getcomposer.org/doc/00-intro.md) in order to install Composer either locally or globally on your computer.

On Windows, you can use the Composer [Windows installer](https://getcomposer.org/Composer-Setup.exe).

> We recommend to install Composer globally on your system.

3. Install Themosis framework
---------------------------------

Open your `Terminal` or `Console` and execute the following command:

```bash
composer create-project themosis/themosis my-project-name
```

This will create a directory called `my-project-name` on your system and automatically download the latest WordPress version along with the latest Themosis framework version and its dependencies.

> Quickly explore the project structure then read the environment setup guide to install your WordPress application.

Next
----
Read the [environment setup guide](https://github.com/themosis/documentation/blob/master/configuration.md)