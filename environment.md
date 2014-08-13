Environment Setup
=================

1. Project structure
2. Install on local environment
3. Install on production environment
4. Environment configuration
5. Shared configuration

1.Project structure
-------------------

- bootstrap/
- config/
- htdocs/
- library/
- vendor/
- .env.local.php
- .env.production.php
- composer.json

Your project comes with a default `wp-config.php` file configured to load the Themosis framework dependencies. Use this file to configure your database, nonce and common WordPress configuration.

> Your project will have the following `vendor` directory installed beside the default WordPress files. This `vendor` directory is created by default by Composer. It stores package dependencies used by the Themosis framework.

Once your `wp-config.php` file is setup, start the default WordPress installation process and log in your WordPress administration. Activate the Themosis framework plugin and the Themosis framework theme.

Visit your project home page and you should be granted with a welcome message. Congratulations! You have installed WordPress and the Themosis framework

> If you don't see the welcome message, check our troubleshooting information page for help.