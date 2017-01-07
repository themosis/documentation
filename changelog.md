Changelog
=========

1.3.0
-----

#### New

- Plugin development.
- Service container (Illuminate/Container).
- Service providers.
- Eloquent ORM and Query Builder.
- Custom and WordPress routes (Illuminate/Router).
- Controllers namespace.
- Middlewares.
- Blade template engine.
- Twig template engine.
- Taxonomy term meta.
- Handle theme and custom plugin translations.
- Finder class.
- Custom post status now handles the `trash` status.
- Taxonomy `get()` method to retrieve taxonomy properties.
- Add Hook API and Filter class.
- Add Whoops package for better stack trace and debug.
- Allow custom attributes on assets using the Asset API.
- Add assets to the Customizer using the Asset API.
- Add custom checkbox label in both Form and Field API.
- Theme Gulp, Webpack, Bower commands.

#### Updates

- Add pagination method to Loop helper class.
- Templates now use WordPress API. Templates can be defined for pages and custom post types (since WordPress 4.7).
- Form select now handles numeric keys and duplicate values.
- The media and collection fields can now handle multiple file types for selection(text, image, application, video, audio).
- Add documentation to translate Blade (Scout) and Twig views with Poedit.
- Allow no versions on assets.
- HTML class attributes are now HTML5 attributes.
- Change post custom fields save method. Now only the Collection and Infinite fields are stored in one serialized meta value.
- Add `js` and `css` asset types to identify external assets.
- Add inline code for any assets.

#### Fixes

- Fix custom taxonomy terms count for custom post type with custom status.
- Fix Taxonomy translated names.
- Fix PostType translated names.
- Fix Metabox template property to show metabox based on page template only.
- Fix registered actions being run twice.
- Fix Page API and sub pages when not using tabs.

#### Deprecated

- PostType `getSlug()` has been removed.
- Form `checkboxes()` has been removed.
- Ajax `run()` method. Use the `listen()` method instead.

1.2.3
-----

#### New

- Metabox make method has an option to define a custom template. Show the metabox only if page template is defined.
- Page make method has an option to change the menu title.
- View instance has a getData() method to retrieve all passed data to it.

#### Updates

- Update class alias management. Revert back to theme only. [READ UPGRADE GUIDE]({{url}}/upgrade)
- Update custom statuses. Custom statuses are shown inside the quick edit panel.
- Update page title tag to h1 for better accessibility (same as core).
- Update custom fields styling on metabox with a side context.
- Update PostType class for plugin development.
- Update Taxonomy class for plugin development.
- Update custom fields to define a save method (currently only used inside metabox containers).

#### Fixes

- Fix User API to not return an empty instance if user do not exists.
- Fix missing value for theme support config file.
- Fix duplicate message on custom sub-page attached to the general settings page.
- Fix alphabetical order for loading admin files.
- Fix theme restrict function with undefined index role.
- Fix route match method to accept lowercase request methods.
- Fix color custom field inside Infinite custom field.

1.2.2
-----

- Fix checkbox field in order to accept value equal to 0.
- Update Option::get() method to retrieve one or a group of options.

1.2.1
-----

- Fix theme PSR-4 autoloading.

1.2.0
-----

#### New

- Action class.
- Asset localize method.
- Configuration API. Access any property at run-time.
- Color custom field.
- Field API.
- Developers can now define custom attributes to custom fields.
- Define custom attributes to checkbox's label tag.
- Custom status for custom post type.
- The PostType get method allows developers to retrieve defined custom post type property.
- User custom fields.
- Loop author method.
- Loop date method.
- @query can receive a WP_Query instance.
- Specify custom ID to metabox.
- Developers can now define custom display names for custom images.
- Developers can specify multiple hostnames per environment.

#### Updates

- Update the theme structure.
- Refactor framework and theme bootstrap code which offers better performance and avoid hook conflicts.
- Improve Option::get() method which can now return a group of settings.
- Move the storage directory at project root.

#### Fixes

- Fix themosis_assets() function directory path.
- Fix infinite field indexes.
- Fix Meta::get() method issue with https rewriting.
- Fix collection field limit property.
- Fix loading of external assets. Developers can define the type of the asset if not found in the URL.
- Fix Loop thumbnail method.
- Fix post meta fields that save others posts meta as well.
- Fix infinite field subfield removal.
- Fix asset duplicates.
- Fix page tab URI when used as sub-page.

#### Deprecated

- The method PostType::getSlug() is deprecated. Use the PostType::get() method.

1.1.3
-----

#### New

- Date input and custom field.
- Number input and custom field.
- Password input and custom field.
- Add a "required" validation rule.
- Themes can now handle all WordPress theme support. The 'title-tag' and 'html5' supports are enabled by default.
- Developers can now customize the display name of their page templates by using key/value pair.

#### Fixes

- Fix a menu script issue where accordion items were not working correctly.
- Fix a GIF display issue on media field.

1.1.2
-----

- Fix form API. Allow developers to specify absolute or relative action attribute value to form tags.

1.1.1
-----

- Fix route API. Array argument was not taken into account for controllers.

1.1.0
-----

#### New

- Share data across all views using View::share() method.
- Add support for view composers. Allows you to run custom code when a specific view is rendered.
- Collection custom field. Add and manage group of media files.
- Load theme custom classes using one loading config file.
- Overwrite default class attribute on custom fields.
- Define a custom view for your metabox.
- Themosis-framework plugin is installed by default inside the mu-plugins directory.
- Loop::postClass() method. Return post class attribute.

#### Updates

- Update Media and Collection field UI with better preview of images and files.
- Update Form::open() method where a developer can define custom nonce values.
- Update metabox custom view. Get access to the WP_Post instance using the $__post variable.
- Update Form::checkbox() method. Work as the Form::radio() method.
- Update Field::checkbox() method accordingly.

#### Fixes

- Fix select custom field issue with numeric index 0.
- Fix access to view data on a custom admin page.
- Fix compiler for {{{ $var or 'default' }}} statement.
- Fix route callbacks and class methods missing parameters when the main query is empty.
- Fix form checkbox method to populate the value attribute.
- Fix metabox options issue with not found indexes.
- Fix Infinite custom field issue with numeric name attributes.

#### Deprecated

- The method Form::checkboxes is deprecated and is replaced by the Form::checkbox method.
- The custom field Field::checkboxes method is deprecated and is replaced by the Field::checkbox method.

1.0.7
-----

- Bump version of all packages to 1.0.7.
- Unified changelog.

1.0.6
-----

- Add support for the default property of the custom fields.
- Fix an issue with composer where other plugins get prefixed.
- Fix the widget path for autoloading.
- Fix taxonomy to use array as post type slugs.
- Fix Asset class issue with external asset path.
- Fix Asset class to use boolean false as dependency argument.

1.0.5
-----

- Fix styling issue on Media custom field.
- Fix issue on Media custom field with images less than 100 pixels.

1.0.4
-----

- Fix route issue on controllers.

1.0.3
-----

- Fix validate method on Page class.

1.0.2
-----

- Fix issue where core classes use the class aliases.

1.0.1
-----

- Infinite and media field support translation.

1.0.0
-----

- Fix Infinite custom field where it duplicates rows.
- Update the Infinite field UI.
- Initial release.