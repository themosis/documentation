Theme development
=================

**Work in progress** guide.

- Introduction
- Structure
- Namespace
- Utilities
    - Gulp JS
    - Webpack
    - Bower
- Translations

Introduction
------------

When you start a new Themosis framework project, Composer also installs the `themosis-theme` theme boilerplate inside the `htdocs/content/themes` directory.

The theme is already setup to work with the Themosis framework core and brings configuration for theme autoloading, config files, service providers, routing and more.

Since release 1.3.0, the theme also bundles GulpJS, WebPack, BrowserSync and Bower in order to help you work with your theme assets (css, js).

> Do not forget to rename your theme folder and remove the dependency to the `themosis-theme` in your root project `composer.json` file.

Structure
---------


