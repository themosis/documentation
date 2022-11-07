Drop-ins
==========

- [Introduction](#introduction)
- [Available drop-ins](#available-drop-ins)
- [Uninstall drop-ins](#uninstall-drop-ins)

## Introduction

Since release `3.0`, the Themosis framework is bundled with a mechanism to manage [WordPress drop-ins](https://developer.wordpress.org/reference/functions/_get_dropins/).

## Available drop-ins

The framework provides for now only the `object-cache.php` drop-in. In order to publish the drop-in file, run the following command:

```php
php artisan vendor:publish --tag=themosis-dropins
```

The above command will copy the provided `object-cache.php` file directly into your application `htdocs/content` folder. The `object-cache.php` file is leveraring the `Illuminate/Cache` package and set object caching for WordPress using the cache driver sets in `config/cache.php` configuration file.

## Uninstall drop-ins

In order to remove a drop-in file from your application, simply call the following command from your terminal:

```php
php artisan dropin:clear
```

The `dropin:clear` command gives you a list of all WordPress drop-ins based on your installation (single or multisite). Simply select the drop-in you want to remove from the list and press enter. This will uninstall the drop-in file from your installation.
