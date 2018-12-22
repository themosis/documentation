Multisite
=========

- [Introduction](#introduction)
- [Installation](#installation)
- [Configuration](#configuration)
    - [Enable multisite installation](#enable-multisite-installation)
    - [Install the theme](#install-the-theme)
    - [Install WordPress](#install-wordpress)
    - [Configure the multisite](#configure-the-multisite)
    - [Activate the theme](#activate-the-theme)

Introduction
------------

Multisite allows you to control a "network" of websites using one single WordPress installation. The Themosis framework is compatible with the multisite feature of WordPress. This guide will help you to start a new multisite project from scratch using the Themosis framework.

The following steps cover both **sub-domains** and **sub-folders** multisite installation.

Installation
------------

Start by downloading a new instance of WordPress and the Themosis framework by running the following command in your `Terminal` or `Console`:

```
composer create-project themosis/themosis my-multisite-project
```

This command will automatically installs the latest WordPress and Themosis framework versions into the `my-multisite-project` folder.

Configuration
-------------

Once your new project is installed, open it in your favorite IDE or code editor. Open the `.env` file and add your database credentials (supposing you already defined your database) and the default URL for your project. Be sure to let the `/cms` URI in the `WP_URL` parameter.

> The default URL will be your main website URL in your multisite installation.

For this example, our `.env` environment file will look like this:

```
APP_ENV = 'local'
APP_DEBUG = true
DATABASE_NAME = "wpmu"
DATABASE_USER = "root"
DATABASE_PASSWORD = "password"
DATABASE_HOST = "localhost"
APP_URL = "http://wpmu.test"
WP_URL = "http://wpmu.test/cms"
```

For more details about environment configuration, please read the [configuration]({{url}}/configuration) guide.

### Enable multisite installation

Now that your environment is setup, open the `config/wordpress.php` configuration file and add the following constant:

```
define('WP_ALLOW_MULTISITE', true);
```

### Install the theme

Before installing WordPress, make sure to install a theme by using the `theme:install` command:

```php
php console theme:install my-theme
```

### Install WordPress

Now that your theme is installed, visit in your browser the URL defined for your WordPress project to start the default WordPress installation. In this example, the website is located at the following address: **http://wpmu.test**

Follow on screen steps and once the process is finished, log into the WordPress administration.

Next, using the left menu in the administration, go to `Tools > Network Setup`. Choose which type of addresses for your websites you would like:

- Sub-domains
- Sub-directories

> Be aware of the warning message regarding sub-directory installation. This means sub-directory is working correctly but you won't be able to create a website under these two URIs: `content` and `cms` because those folders are used by WordPress and the Themosis framework.

For this example, we'll choose the **sub-domain** installation type. You can also modify your network title and super-administrator email address if you want. You can keep default values otherwise.

Click the **Install** button.

### Configure the multisite

> On some installation you might get a warning message with a `timeout` issue when WordPress tried to access a random sub-domain address. Don't worry, this is because you haven't a wild-card defined for your sub-domains.

WordPress is now telling you to modify your configuration by adding some new constants and by updating the `.htaccess` file.

Open the `config/wordpress.php` file and add the mentioned constants. In our example, you'll add those lines:

```php
define('MULTISITE', true);
define('SUBDOMAIN_INSTALL', true); // Set to false if you selected the "Sub-directories" installation.
define('DOMAIN_CURRENT_SITE', 'wpmu.test'); // Main domain. Same as defined in your .env file without the http protocol.
define('PATH_CURRENT_SITE', '/');
define('SITE_ID_CURRENT_SITE', 1);
define('BLOG_ID_CURRENT_SITE', 1);
```

The `DOMAIN_CURRENT_SITE` constant value is dependent of your environment. You might want to move its value to your `.env` file instead. Update your `.env` file and add the domain current site value like so:

```
WP_CURRENT_SITE = 'wpmu.test'
```

Update your `config/wordpress.php` file to take advantage of the environment variable:

```php
define('DOMAIN_CURRENT_SITE', env('WP_CURRENT_SITE'));
```

Then open the `.htaccess` file and copy/paste the code shown in your WordPress screen.

Finally click the small **Log in** link at the bottom of your WordPress network installation screen to re-connect and log in back to the administration.

### Activate the theme

In the case where you theme is not setup as the default, you might need to "network" activate it.

On the top left of your screen, go to **My Sites** > **Network Admin** > **Themes**

Find your theme and click on the **Network Enable** link in order to make it available on your network.

Then by using the **My Sites** navigation, go to your main website and now under **Appearance** > **Themes**, you can activate your theme.

**Congratulations**, you can now develop your multisite project using all the WordPress and framework APIs.