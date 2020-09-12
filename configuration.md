Configuration
=============

- [Introduction](#introduction)
- [Environment configuration](#environment-configuration)
    - [Default environment configuration](#default-configuration)
    - [Retrieving environment configuration](#retrieving-environment-configuration)
    - [Determining the current environment](#determining-the-current-environment)
- [Install WordPress](#install-wordpress)
    - [Fine-tune WordPress configuration](#fine-tune-wordpress-configuration)
- [Access configuration values](#access-configuration-values)
    - [Retrieve all properties](#retrieve-all-properties)

Introduction
------------

Before digging into the WordPress configuration, please note that all of the configuration files for the Themosis framework are stored in the `config` directory located at project root.

The folder is containing a list of files, each dedicated to some features or packages of your application (application, cache, database, WordPress, ...). Each file is documented so feel free to read the files and get to know the options available to you.

The root `config` directory is storing global or shared options for your application. But both theme and custom plugins you develop also store dedicated configuration files into a `config` directory.

Let's first explore the environment configuration in order to setup our application and then proceed to the WordPress installation.

Environment Configuration
-------------------------

The Themosis framework has always provided a way to define environment variables. Since release 2.0, the framework has changed its environment management and simplified it by defining a single `.env` file at project root.

If you install the Themosis framework with Composer, the repository holds a default `.env.sample` file which is automatically renamed to `.env` upon installation for you.

Thanks to this `.env` file, you're now able to store critical data for your application services out of the web server public root.

In order to complete your application installation, you need to configure it by using this `.env` file.

### Default Configuration

Open your project `.env` file from your code editor or IDE. The `.env` file contains the following default variables:

```bash
APP_ENV = "local"
APP_DEBUG = true
DATABASE_NAME = "your-database-name"
DATABASE_USER = "your-database-user"
DATABASE_PASSWORD = "your-database-password"
DATABASE_HOST = "localhost"
APP_URL = "http://local.test"
WP_URL = "http://local.test/cms"
```

The application environment is set for `local` development with debugging option set to `true` by default.

> If you remove the `APP_ENV` variable, its value is set to `production` by default.

In order to install the application, you need to fill in the database options as well as defining your local virtual host URL in the `APP_URL` and `WP_URL` configurations.

The `APP_URL` variable is the base URL of your application and the `WP_URL` is the base URL of the WordPress directory. Here WordPress is installed in the sub-directory `cms`, so make sure to keep the `/cms` fragment in your `WP_URL` value.

> You can use [Laravel Homestead](https://laravel.com/docs/5.7/homestead) has a local development tool for your projects.

### Retrieving environment configuration

Environment variables defined in the `.env` file are parsed by the [vlucas/phpdotenv](https://github.com/vlucas/phpdotenv) package. The package is loading those variables into the `$_ENV` and `$_SERVER` superglobals of PHP making them available anywhere in your application.

In order to retrieve a value from your environment configuration file, you can use the `env()` helper function:

```php
$debug = env('APP_DEBUG');
```

You can also specify a default value if the requested environment variable does not exist:

```php
$custom = env('CUSTOM_VAR', 'default value');
```

### Determining the current environment

The current application environment is defined by the `APP_ENV` variable from your `.env` file. You can access this value by using the `environment()` method of the `App` facade like so:

```php
$env = App::environment();
```

You may also pass arguments to the environment method to check if the environment matches a given value. The method will return true if the environment matches any of the given values:

```php
if (App::environment('local')) {
    // Local environment...
}

if (App::environment(['local', 'staging'])) {
    // Environment is either "local" or "staging"...
}
```

Install WordPress
-----------------

Now that your `.env` default variables are filled in, you can open your browser and visit your application at the URL defined in `APP_URL` environment variable.

Follow steps on screen in order to complete the WordPress installation.

### Fine-tune WordPress configuration

The framework now bundles a `wordpress.php` configuration file stored in the `config` directory.

Instead of populating the `wp-config.php` file, you can now add all your WordPress constants into the `config/wordpress.php` file.

From that file, you can also access your environment variables with the use of the `env()` helper function.

Make sure to define the [WordPress authentication keys and salts](https://api.wordpress.org/secret-key/1.1/salt/) constants before developing or deploying your application:

```php
define('AUTH_KEY', 'put your unique phrase here');
define('SECURE_AUTH_KEY', 'put your unique phrase here');
define('LOGGED_IN_KEY', 'put your unique phrase here');
define('NONCE_KEY', 'put your unique phrase here');
define('AUTH_SALT', 'put your unique phrase here');
define('SECURE_AUTH_SALT', 'put your unique phrase here');
define('LOGGED_IN_SALT', 'put your unique phrase here');
define('NONCE_SALT', 'put your unique phrase here');
```

Access configuration values
---------------------------

The Themosis framework is now using the `illuminate/config` package for managing the application configuration.

You can access the configuration values by using the `config()` helper function:

```php
$name = config('app.name');
```

The `Config` facade is also still available. Use the `get()` method in order to retrieve a configuration value:

```php
$charset = Config::get('app.charset');
```

Both the `config()` helper function and the `Config` facade `get()` method can define a second parameter. The second parameter is used to return a default value if the configuration value requested does not exist:

```php
$name = config('app.name', 'My Application');
$env = Config::get('app.env', 'staging');
``` 

The configuration API is using a dot syntax in order to look through your configuration files. In the example above, the `app` fragment before the dot is indicating the `app.php` file stored into the `config` root directory. The `name` fragment is configuration key name in that file.

You can nest configuration values. For example, the `app.php` file contains the following configuration for WordPress:

```php
$wp_directory = config('app.wp.dir');
```

### Retrieve all properties

By providing the configuration file name without the `.php` extension, you can retrieve all of its configuration values.

Let's grab all properties of the theme's `theme.php` configuration file:

```php
$all = config('theme');
```

The above code is fetching all properties from the `theme.php` file stored in the theme `config` folder.

### Configuration load order
Please note, that if configuration files with the same name are defined under both the root `config` directory as well as plugin or theme folders Themosis does not merge their contents, but the active plugins' config files override the root configuration, and the active theme's configuration files override both. However WordPress loads the global configuration first, active plugins' config files after and the active theme's config at last, which means the value under certain configuration keys would be different at different times during each page load. This can cause unexpected behaviour as it may be hard to predict which configuration file will be effective when accessing the configuration in some parts of your application. Therefore it is best to use unique configuration file names for the root configurations and all active Themosis Plugins and Theme.

Next
----
Read the [application structure guide]({{url}}/structure)
