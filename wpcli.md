4. Install WP-CLI
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