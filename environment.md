Environment Setup
=================

- [Introduction](#introduction)
- [Load your environment](#load-your-environment)
- [Shared environment configuration](#shared-environment-configuration)
- [Install WordPress](#install-wordpress)

Introduction
------------

The Themosis framework has its own way for defining WordPress configurations. It's done on purpose to facilitate collaboration. By default, the Themosis framework comes with a `local` and a `production` environments pre-configured.

You'll start by registering your database credentials and application URLs into a `.env.{environment}` file located in the root directory of your project. Then you'll be able to define your environment configurations by modifying files located in the `config` directory.

> Opening your project in a text editor or IDE should show you a default `.env` file: `.env.local`. The framework release is now leveraging the [DotEnv PHP package](https://github.com/vlucas/phpdotenv).

Load your environment
---------------------

Let's start by installing your WordPress application on a local environment.

> Follow the same steps for a remote/production environment or any custom ones.

### 1 - Set your credentials and URLs

Open the default `.env.local` file located in the root of your project. Fill in the values with your local database credentials and specify your local virtual host URLs.

```
DB_NAME = "your_database_name"
DB_USER = "database_username"
DB_PASSWORD = "database_password"
DB_HOST = "localhost"
WP_HOME = "http://my-website.dev"
WP_SITEURL = "http://my-website.dev/cms"
```

> WordPress is defined as a dependency and is loaded by the framework inside the `cms` directory located in the web root folder `htdocs`. Make sure to always define the `WP_SITEURL` value with `/cms` appended at the end.

> The `DB_NAME`, `DB_USER`, `DB_PASSWORD`, `DB_HOST`, `WP_HOME`, `WP_SITEURL` variables are all required. You can retrieve those values any time by using the PHP function `getenv('DB_NAME');`.

Once your credentials are registered, we need to identify the local environment.

### 2 - Identify your local environment

2 methods are available in order to identify your environment:

- By looking at your machine/computer `hostname`
- By looking after a server environment variable

#### Identify environment using the hostname

> Default method used by the framework

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

Look at the first displayed line `Host Name`.

Once you get your hostname, open the `environment.php` file and replace the value of the `local` key:

```php
<?php

/*----------------------------------------------------*/
// Define your environments
/*----------------------------------------------------*/
return [

    'local'      => 'WRITE YOUR HOSTNAME HERE',
    'production' => 'remote machine hostname'

];
```

> The key defined in the `environment.php` file is used to load the `.env.{$environment}` file and also the `{$environment}.php` file located in the `config/environments` directory.

##### Multiple hostnames

In some scenarios, you'll probably need to define several hostnames per environment. In order to do so, you can pass an array of hostnames to each environment key like so:

```php
<?php

/*----------------------------------------------------*/
// Define your environments
/*----------------------------------------------------*/
return [

    'local'      => ['computer1', 'computer2', 'computer3'],
    'production' => 'remote machine hostname'

];
```

#### Identify environment using a server environment variable

If you have total control of your web server and are able to set environment variable, you can use a closure in order to load the application environment configuration.

##### Apache

Normally you put a `SetEnv` statement inside your `<VirtualHost>` directive or if you don't have access to this configuration, you can add it inside your `.htaccess` file if allowed.

Specify a local environment variable for Apache:

```
SetEnv varName varValue
```

Now inside your `environment.php` file, in place of an array, you'll replace your code by a Closure like so:

```php
<?php

return function()
{
    // Check for the environment variable
    if ('varValue' === getenv('varName'))
    {
        // Return the environment file slug name: .env.{$slug}
        return 'local';
    }

    // Else if no environment variable found... it might be a production environment...
    return 'production';
};
```

In the example above, if the `getenv('varName')` exists, this will load the the `local` environment file: `.env.local` located at the root of your application.

##### Nginx

Nginx has a `ENV` function but this apparently only works in the main context of your web server. In order to specify an environment variable per `server` context, you will use the `fastcgi_param` statement.

```
server {
    location ~ \.php$ {
    	include        fastcgi_params;
        fastcgi_pass   themosis.dev:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name";

        ###############################
        # Your environment variable   #
        ###############################
        fastcgi_param varName varValue;
    }
}
```

For Nginx, your environment variable is accessible by using `$_SERVER[]`. So inside your `environment.php` file, you'll write:

```php
<?php

return function()
{
    // Check for the environment variable
    if ('varValue' === $_SERVER['varName'])
    {
      // Return the environment file slug name: .env.{$slug}
      return 'local';
    }

    // Else if no environment variable found... it might be a production environment...
    return 'production';
};
```

Now that your environment is defined, you can configure it to your needs.

### 3 - Add configurations

By default, the Themosis framework defines a `local` environment and loads the default `local.php` file stored in the `config/environments` folder:

```php
<?php

/*----------------------------------------------------*/
// Local config
/*----------------------------------------------------*/
// Database
define('DB_NAME', getenv('DB_NAME'));
define('DB_USER', getenv('DB_USER'));
define('DB_PASSWORD', getenv('DB_PASSWORD'));
define('DB_HOST', getenv('DB_HOST') ? getenv('DB_HOST') : 'localhost');

// WordPress URLs
define('WP_HOME', getenv('WP_HOME'));
define('WP_SITEURL', getenv('WP_SITEURL'));

// Jetpack
define('JETPACK_DEV_DEBUG', true);

// Encoding
define('THEMOSIS_CHARSET', 'UTF-8');

// Development
define('SAVEQUERIES', true);
define('WP_DEBUG', true);
define('WP_DEBUG_DISPLAY', true);
define('SCRIPT_DEBUG', true);

// Themosis framework
define('THEMOSIS_ERROR', true);
define('BS', true);
```

This is the pre-configured local configuration file. You can add as many as you want configurations to this file. These are only available for your `local` environment.

> Notice the removed constants `THEMOSIS_ERROR_REPORT`, `THEMOSIS_ERROR_DISPLAY` and `THEMOSIS_ERROR_SHUTDOWN` which are no longer used. The framework now integrates error reporting with the [Whoops package](http://filp.github.io/whoops/).

Finally, note the `BS` constant which is mainly used inside the new theme to output BrowserSync script for your local development.

Shared environment configuration
--------------------------------

Some WordPress configurations are the same whatever environment you're using, like the authentication salts,...

Common configuration can be found into the `shared.php` file stored in the `config` directory.

In order to define shared configuration, you can edit this `shared.php` file. Fill in default configuration or add custom ones.

In order to finish our local configuration, open the `shared.php` file and fill in the authentication salts:

```php
<?php

/*----------------------------------------------------*/
// Authentication unique keys and salts
/*----------------------------------------------------*/
define('AUTH_KEY',         'put your unique phrase here');
define('SECURE_AUTH_KEY',  'put your unique phrase here');
define('LOGGED_IN_KEY',    'put your unique phrase here');
define('NONCE_KEY',        'put your unique phrase here');
define('AUTH_SALT',        'put your unique phrase here');
define('SECURE_AUTH_SALT', 'put your unique phrase here');
define('LOGGED_IN_SALT',   'put your unique phrase here');
define('NONCE_SALT',       'put your unique phrase here');

```

Install WordPress
-----------------

Once your environment is setup, open your browser and start the default WordPress installation process.

> Note: By default, the bundled theme called `themosis-theme` is activated upon installation to avoid the famous "white screen of death". Please note that we encourage you to rename the theme to reflect your project. When you rename your theme, WordPress lost its tracks. Be sure to log in to the WordPress administration and activate your renamed theme.

Visit your project home page and you should be granted with a welcome message. Congratulations! You have installed WordPress and the Themosis framework.

Next
----
Read the [framework guide]({{url}}/framework)
