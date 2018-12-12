Metabox
=======

- [Basic usage](#basic-usage)
    - [Set a custom title](#set-a-custom-title)
    - [Set the context](#set-the-context)
    - [Set the priority](#set-the-priority)
- [Custom fields](#custom-fields)
    - [Add custom fields](#add-custom-fields)
    - [Add custom fields groups](#add-custom-fields-groups)
    - [Prefixing](#prefixing)
- [Validation](#validation)
- [Localization](#localization)
- [Customize the metabox](#customize-the-metabox)
- [Send data to your metabox](#send-data-to-your-metabox)

Basic usage
-----------

As the name suggests, the `Metabox` class helps you build a custom WordPress metabox. A metabox is a UI container, attached to one or more post types, where you can define and display any custom content.

By default, when you create a metabox with the framework, it is setup to display custom fields. But you are free to add any content into a metabox. See below for customizing the metabox.

In order to create a metabox for your post, page or custom post type, you have to use the `make` method and pass it a unique identifier and a screen parameter. Then, you register the metabox by calling to the `set()` method like so:

```php
use Themosis\Support\Facade\Metabox;

Metabox::make('properties', 'post')->set();
```

The above example will display a metabox with a title of "Properties" in a WordPress post edit screen. It will also display an empty layout as it is configured to display custom fields by default.

You can also attach a metabox to multiple post types by passing an array as a second parameter:

```php
use Themosis\Support\Facades\Metabox;

Metabox::make('properties', ['post', 'page'])->set();
```

### Set a custom title

When registering a metabox, you first need to pass a unique identifier. By default, the class is using that id as the metabox title. In order to provide a custom title for your metabox, you may use the `setTitle()` method like so:

```php
Metabox::make('properties', 'post')
    -setTitle(_x('Product details', 'metabox', 'textdomain'))
    ->set();
```

### Set the context

By default, a metabox context is set to `normal`. If you want to change the metabox context (display area), you can use the `setContext()` method:

```php
Metabox::make('properties', 'post')
    ->setContext('side')
    ->set();
```

### Set the priority

By default, a metabox priority is set to `default`. If you want to change the metabox priority, you may call the `setPriority()` method:

```php
Metabox::make('properties', 'post')
    ->setPriority('high')
    ->set();
```

By default, the metabox is configured to handle custom fields. If you want to create metabox with a custom user interface, read the section [customize the metabox](#customize-the-metabox) below.

Custom fields
-------------

Since release 2.0, the framework is bundled with new custom fields managed by a RestAPI and [ReactJS](https://reactjs.org/) components. The metabox class leverages the Field class in order to add custom fields.

> Please note that not all form fields are available on a metabox. Please see the [field guide]({{url}}/field) for more details.

### Add custom fields

In order to add custom fields to a metabox, use the `add` method:

```php
use Themosis\Support\Facades\Field;

Metabox::make('properties', 'post')
    ->add(Field::text('author'))
    ->add(Field::text('isbn'))
    ->add(Field::integer('publication_year'))
    ->set();
```

The above example will display a metabox with two text fields and a number field. The new Field API for metabox is now handled through JavaScript only and built with ReactJS components.

In order for a user to save the data handled by the custom fields, each metabox displays a "Save" button below. If the user click on the main "Publish" or "Update" button for the post, the metabox custom fields data will not be saved. Custom fields data is now saved through a custom RestAPI.

> Note: the metabox and field RestAPI is using the default `api` middleware group. Be sure to not change the group name.

### Add custom fields groups

It is also possible to organize custom fields by group using the `add` method and by passing a section instance with custom fields:

```php
use Themosis\Support\Facades\Field;
use Themosis\Support\Facades\Metabox;
use Themosis\Support\Section;

Metabox::make('properties', 'post')
    ->add(new Section('general', 'General', [
        Field::text('author'),
        Field::text('publisher')
    ]))
    ->add(new Section('details', 'Details', [
        Field::text('isbn'),
        Field::media('cover')
    ]))
    ->set();
```

The above example will display fields organized within 2 tabs: "General" and "Details". Each time you define a section, you have to provide a unique identifier as the first parameter and a display label as the second parameter. The section fields are then defined into the third parameter as an array.

> You need to define at least 2 sections of fields in order to trigger the tabs layout.

In order to customize your individual fields, please read the documentation on the [field guide]({{url}}/field) for more information.

### Prefixing

The new field API is automatically prefixing custom fields names. By default if you add a text field with a name of `author`, it stores the value with a meta key of `th_author` in the post meta table.

If you want to change the default prefix for your metabox fields, you may use the `setPrefix` method:

```php
Metabox::make('properties', 'post')
    ->add(Field::text('author'))
    ->setPrefix('wp_')
    ->set();
```

> Prefixing is enabled by default to avoid any conflicts with core reserved terms and data coming from third party plugins. If you do not want to use prefix, simply pass an empty string to the `setPrefix` method.

Validation
----------

The new field API is leveraging the [illuminate/validation](https://laravel.com/docs/5.7/validation) package from Laravel in order to validate custom fields data. The new user interface also provides error display on each field and also on tabs if you're using groups.

In order to validate data, you need to define a set of `rules` on a per field basis. Here is an example of a text field:

```php
Metabox::make('settings', 'Parameters')
    ->add(Field::text('author', [
        'rules' => 'required|min:3'
    ]))
    ->set();
``` 

The above example sets the author text field as a required value and must have a length of at least 3 characters. If the validation fails, the field data is not saved and the metabox is displaying an error message below the field.

For a list of available validation rules, please read [the official documentation](https://laravel.com/docs/5.7/validation#available-validation-rules).

> When a validation fails, the metabox is displaying a "Saved with errors" message in its footer. This means that valid field data is saved in the database while failed one is not.

### Customize error message

//

### Customize validation attribute

//

Localization
------------

//

Retrieve data
-------------

In order to retrieve the custom fields data, you can use the core `get_post_meta` function. Here is an example based on an `author` custom text field:

```php
$author = get_post_meta($post_id, 'th_author', true);
```

> Please note that custom fields meta key names are using a [prefix](#prefixing) by default.

Customize the metabox
---------------------

You can customize the look and feel and the behaviour of your metabox by defining a custom view (and why not view composers...).

You can pass a custom view to your metabox using the 4th argument of the `Metabox::make()` method like so:

```php
// Code below written inside the 'admin/metabox.php' file.
// File stored inside the 'views/metabox/custom.blade.php'.
$view = View::make('metabox.custom');

Metabox::make('Properties', 'post', ['priority' => 'high'], $view);
```

Inside the view file of your metabox you have access to "special" variables by default:

- **$__fields**: Gives you an array of registered fields with your metabox.
- **$__metabox**: Gives you access to your metabox instance.
- **$__post**: Gives you access to the current post instance object (WP_Post).

This allows you to customize as you want the look of your metabox. By also using `View::composer()` method, you might also perform specific actions when the metabox is rendered.

In case you needed to customize the metabox output but still need to output the core custom fields, simply add this code snippet inside your metabox view:

```php
<!-- Default Themosis metabox view -->
<table class="form-table themosis-metabox">
    <tbody>
        @each('_themosisMetaboxRow', $__fields, 'field')
    </tbody>
</table>
```

Send data to your metabox
-------------------------

Each time you create a metabox, a view file is attached to it. If you need to customize the metabox and need to use more data, you can pass data to your metabox view by using the `with` method like so:

```php
$metabox = Metabox::make('Infos', 'post')->set();

// Send a custom data to the metabox view
$metabox->with('key', 'value');
```

Of course, this methods is only useful if you specify a custom view for your metabox. You can also pass an array of `key/value` pairs to the `with` method.

Next
----

* [Meta guide]({{url}}/meta)
* [Taxonomy guide]({{url}}/taxonomy)
* [Page guide]({{url}}/page)
