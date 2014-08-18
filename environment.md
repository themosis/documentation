Environment Setup
=================

1. Introduction
2. Install on local environment
3. Install on production environment
4. Shared environment configuration

1.Introduction
--------------

The Themosis framework has its own way for defining WordPress configurations. It's done on purpose to facilitate collaboration. By default, the Themosis framework comes with a `local` and a `production` environment.

You'll start by registering your database credentials and application URLs into a `.env.{environment}.php` file located in the root directory of your project. Then you'll be able to define your environment configurations by modifying files located in the `config` directory.

> Opening your project in a text editor or IDE should show you two default `.env` files: `.env.local.php`, `.env.production.php`.

Let's start by installing your WordPress application on a local environment.

2.Install on local environment
------------------------------

#### 1 - Set your credentials and URLs

Open the default `.env.local.php` file located in the root of your project. Fill in the values with your local database credentials and specify your local virtual host.

```php
<?php

/*----------------------------------------------------*/
// Local environment vars
/*----------------------------------------------------*/
return array(

    'DB_NAME'		=> 'your_database_name',
    'DB_USER'		=> 'database_username',
    'DB_PASSWORD'	=> 'database_password',
    'DB_HOST'		=> 'localhost',
    'WP_HOME'		=> 'http://my-website.dev',
    'WP_SITEURL'	=> 'http://my-website.dev/cms'

);
```

> WordPress is defined as a dependency and is loaded by the framework inside the `cms` directory located in the web root folder `htdocs`. Make sure to always define the `WP_SITEURL` value with `/cms` appended at the end.

> The `DB_NAME`, `DB_USER`, `DB_PASSWORD`, `DB_HOST`, `WP_HOME`, `WP_SITEURL` variables are all required. You can retrieve those values any time by using the PHP function `getenv('DB_NAME');`.

Once your credentials are registered, we need to identify the local environment.

#### 2 - Identify your local environment

In order for the framework to identify your `local` environment, you have to register your machine/computer `hostname` in the `environment.php` file located inside the `config` directory.

##### Find hostname on *NIX

Open your Terminal and simply run the following command:
```bash
hostname
```

##### Find hostname on Windows

Open your Console and run the following command:
```bash
ipconfig/all
```

Look at the first displayed line `hostname`.

Once you get your hostname, open the `environment.php` file and replace the value of the `local` key:

```php
<?php

/*----------------------------------------------------*/
// Define your environments
/*----------------------------------------------------*/
return array(

    'local'             => 'WRITE YOUR HOSTNAME HERE',
    'production'        => 'remote machine hostname'

);
```





Once your environment is setup, start the default WordPress installation process and log in your WordPress administration. Activate the Themosis framework plugin and the Themosis framework theme.

Visit your project home page and you should be granted with a welcome message. Congratulations! You have installed WordPress and the Themosis framework.

> If you don't see the welcome message, check our troubleshooting information page for help.