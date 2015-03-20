Multisite
=========

1. Installation
2. Cleanup default URL
3. Use domain mapping

1. Installation
---------------

Mutlisite allows you to control a "network" of websites using one single WordPress instance. The Themosis framework is compatible with the multisite feature of WordPress. This guide will help you to start a new multisite project from scratch using the Themosis framework.

The following steps cover both **sub-domains** and **sub-folders** multisite installation.

### A. Create a new project

Start by downloading a new instance of WordPress and the Themosis framework by running the following command in your `Terminal` or `Console`:

```
composer create-project themosis/themosis my-multisite-project
```

This command will automatically installs the latest WordPress and Themosis framework versions into the `my-multisite-project` folder.

### B. Configure your environment

Once your new project is installed, open it in your favorite IDE or code editor. Open the `.env.local.php` file and add your database credentials (supposing you already defined your database) and the default URL for your project. Be sure to let the `/cms` URI in the `WP_SITEURL` parameter.

> The default URL will be your main website URL in your multisite installation.

For this example, our `.env.local.php` environment file will look like this:

```php
<?php

/*----------------------------------------------------*/
// Local environment vars
/*----------------------------------------------------*/
return array(

    'DB_NAME'       => 'wpmu',
    'DB_USER'       => 'root',
    'DB_PASSWORD'   => '',
    'DB_HOST'       => 'localhost',
    'WP_HOME'       => 'http://wpmu.dev',
    'WP_SITEURL'    => 'http://wpmu.dev/cms'

);
```
Then open the `environment.php` file and specify your local environment server hostname or define an anonymous function (Closure) to let the framework know which environment file to load. Check the environment guide for more detailed explanations: [http://framework.themosis.com/docs/environment/](http://framework.themosis.com/docs/environment/)

### B. Allow your instance to run the multisite installation

Now that your environment is setup, look after the `shared.php` file located in the `config` directory and open it.

First add the WordPress salt keys by following this URL: [https://api.wordpress.org/secret-key/1.1/salt/](https://api.wordpress.org/secret-key/1.1/salt/)

Then simply add the following constant to your `shared.php` file:

```
define('WP_ALLOW_MULTISITE', true);
```

### C. Install WordPress

Visit in your browser the URL defined for your WordPress project to start the default WordPress installation. In this example, the website is located at the following address: **http://wpmu.dev**

Follow on screen steps and once the process is finished, log in the WordPress administration.

Next, using the left menus in the administration, go to `Tools>Network Setup`. Choose which type of addresses for your websites you would like:
- Sub-domains
- Sub-directories

> Be aware of the warning message regarding sub-directory installation. This means sub-directory is working correctly but you won't be able to create a website under these two URIs: `content` and `cms` because those folders are used by WordPress and the Themosis framework.

For this example, we'll choose **Sub-domains** installation type. You can also modify your network title and super-administrator email address if you want. You can keep default values otherwise.

Click the **Install** button.

### D. Update your instance configuration

> On some installation you might get a warning message with a `timeout` issue when WordPress tried to access a random sub-domain address. Don't worry, this is because you haven't a wilcard defined for sub-domains.

WordPress is now telling you to modify your configuration by adding some new constants and by updating the `.htaccess` file.

> We'll update the documentation regarding an Nginx installation.

Open your `shared.php` file and add the mentioned constants. In our example, you'll add those lines:

```php
define('MULTISITE', true);
define('SUBDOMAIN_INSTALL', true); // Set to false if you selected the "Sub-directories" installation.
define('DOMAIN_CURRENT_SITE', 'wpmu.dev'); // Main domain. Same as defined in your .env file without the http protocol.
define('PATH_CURRENT_SITE', '/');
define('SITE_ID_CURRENT_SITE', 1);
define('BLOG_ID_CURRENT_SITE', 1);
```