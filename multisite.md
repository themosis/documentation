Multisite
=========

- [Installation](#installation)
	- [Create a new project](#create-a-new-project)
	- [Configure your environment](#configure-your-environment)
	- [Allow your instance to run the multisite installation](#allow-your-instance-to-run-the-multisite-installation)
	- [Install WordPress](#install-wordpress)
	- [Update your instance configuration](#update-your-instance-configuration)
	- [Activate your themosis-theme](#activate-your-themosis-theme)
- [Cleanup default URL](#cleanup-default-url)

Installation
------------

Multisite allows you to control a "network" of websites using one single WordPress instance. The Themosis framework is compatible with the multisite feature of WordPress. This guide will help you to start a new multisite project from scratch using the Themosis framework.

The following steps cover both **sub-domains** and **sub-folders** multisite installation.

### Create a new project

Start by downloading a new instance of WordPress and the Themosis framework by running the following command in your `Terminal` or `Console`:

```
composer create-project themosis/themosis my-multisite-project
```

This command will automatically installs the latest WordPress and Themosis framework versions into the `my-multisite-project` folder.

### Configure your environment

Once your new project is installed, open it in your favorite IDE or code editor. Open the `.env.local.php` file and add your database credentials (supposing you already defined your database) and the default URL for your project. Be sure to let the `/cms` URI in the `WP_SITEURL` parameter.

> The default URL will be your main website URL in your multisite installation.

For this example, our `.env.local.php` environment file will look like this:

```php
<?php
/*----------------------------------------------------*/
// Local environment vars
/*----------------------------------------------------*/
return [
    'DB_NAME'     => 'wpmu',
    'DB_USER'     => 'root',
    'DB_PASSWORD' => '',
    'DB_HOST'     => 'localhost',
    'WP_HOME'     => 'http://wpmu.dev',
    'WP_SITEURL'  => 'http://wpmu.dev/cms'
];
```
Then open the `environment.php` file and specify your local environment server hostname or define an anonymous function (Closure) to let the framework know which environment file to load. Check the environment guide for more detailed explanations: [http://framework.themosis.com/docs/environment/](http://framework.themosis.com/docs/environment/)

### Allow your instance to run the multisite installation

Now that your environment is setup, look after the `shared.php` file located in the `config` directory and open it.

First add the WordPress salt keys by following this URL: [https://api.wordpress.org/secret-key/1.1/salt/](https://api.wordpress.org/secret-key/1.1/salt/)

Then simply add the following constant to your `shared.php` file:

```
define('WP_ALLOW_MULTISITE', true);
```

### Install WordPress

> Before installing WordPress, we suggest you to always rename the `themosis-theme` folder with a name that corresponds to your project. For this documentation, we keep the default name in order to keep it simple.

Visit in your browser the URL defined for your WordPress project to start the default WordPress installation. In this example, the website is located at the following address: **http://wpmu.dev**

Follow on screen steps and once the process is finished, log in the WordPress administration.

Next, using the left menus in the administration, go to `Tools>Network Setup`. Choose which type of addresses for your websites you would like:

- Sub-domains
- Sub-directories

> Be aware of the warning message regarding sub-directory installation. This means sub-directory is working correctly but you won't be able to create a website under these two URIs: `content` and `cms` because those folders are used by WordPress and the Themosis framework.

For this example, we'll choose **Sub-domains** installation type. You can also modify your network title and super-administrator email address if you want. You can keep default values otherwise.

Click the **Install** button.

### Update your instance configuration

> On some installation you might get a warning message with a `timeout` issue when WordPress tried to access a random sub-domain address. Don't worry, this is because you haven't a wild-card defined for sub-domains.

WordPress is now telling you to modify your configuration by adding some new constants and by updating the `.htaccess` file.

> We'll update the documentation regarding an Nginx installation.

Open your `shared.php` (and not wp-config.php) file and add the mentioned constants. In our example, you'll add those lines:

```php
define('MULTISITE', true);
define('SUBDOMAIN_INSTALL', true); // Set to false if you selected the "Sub-directories" installation.
define('DOMAIN_CURRENT_SITE', 'wpmu.dev'); // Main domain. Same as defined in your .env file without the http protocol.
define('PATH_CURRENT_SITE', '/');
define('SITE_ID_CURRENT_SITE', 1);
define('BLOG_ID_CURRENT_SITE', 1);
```

Then open the `.htaccess` file and copy/paste the code shown in your WordPress screen.

Finally click the small **Log in** link at the bottom of your WordPress network installation screen to re-connect and log in back to the administration.

### Activate your themosis-theme

By default the `themosis-theme` won't be available inside the `Appearance->Themes` panel. If you visit that screen you should see an admin message telling that WordPress is looking after the twenty-something theme.

In order to get our `themosis-theme` available, we have to **"network activate it"**.

On the top left of your screen, go to **My Sites** > **Network Admin** > **Themes**

The `themosis-theme` is listed. Click on the **Network Enable** link in order to make your theme available on your network.

Then by using the **My Sites** navigation, go to your main website and now under **Appearance** > **Themes**, you can activate your `themosis-theme`.

Now if you visit the front-end of your website, everything seems to work correctly...except that if you look at the URL in your browser, all requests have the `cms/` URI fragment... If you remove the `cms/` URI, note that all requests work.

> The issue is not a framework one but a WordPress core one. This might be fixed on a future update of WordPress. [A ticket has been opened on WordPress trac](https://core.trac.wordpress.org/ticket/33909), don't hesitate to contribute to it so it can be merged into core.

Cleanup default URL
-------------------

In order to fix the `cms/` URI issue, we need to change the `home` value stored in the `wp_options` table of your database. Connect to your database using your favorite tool and look after the `home` _option\_name_ key inside the `wp_options` table.

> Your table name might be different if you specified another table prefix in your configuration files.

The `home` value is the default URL of your website, which by mistake is setup as the same as the WordPress URL. Edit the value by simply removing the `cms` URI and keep the last trailing slash.

In our example, the value should look like this one:

```mysql
http://wpmu.dev/
```

Visit the front-end and everything should work as expected.

**Congratulations, you can now develop your multisite project using all the WordPress and framework APIs.**

**If you encounter an issue, don't hesitate to post it on Github: [https://github.com/themosis/framework/issues](https://github.com/themosis/framework/issues)**