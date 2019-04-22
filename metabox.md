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
    - [Get metabox translation](#get-metabox-translation)
- [Retrieve data](#retrieve-data)
- [Customize the metabox](#customize-the-metabox)
    - [Class callback](#class-callback)
    - [Callback arguments](#callback-arguments)
- [Display based on capability](#display-based-on-capability)
- [Display based on template](#display-based-on-template)

Basic usage
-----------

As the name suggests, the `Metabox` class helps you build a custom WordPress metabox. A metabox is a UI container, attached to one or more post types, where you can define and display any custom content.

By default, when you create a metabox with the framework, it is setup to display custom fields. But you are free to add any content into a metabox. See below for customizing the metabox.

In order to create a metabox for your post, page or custom post type, you have to use the `make` method and pass it a unique identifier and a screen parameter. Then, you register the metabox by calling to the `set()` method like so:

```php
use Themosis\Support\Facades\Metabox;

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
    ->setTitle(_x('Product details', 'metabox', 'textdomain'))
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
    ->setPrefix('wp_')
    ->add(Field::text('author'))
    ->set();
```

Make sure to call the method before adding your custom fields.

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

By default, validation messages for the overall application are defined into a `validation.php` file stored in a directory with a name equivalent to the current loaded locale within the `resources/languages/` directory at project root.

If you want to customize the errors messages for your metabox fields, you can pass a `messages` property to each field with an array containing key value pairs. The key is the validation rule and the value is the message.

Let's customize the fields from previous example:

```php
Metabox::make('settings', 'Parameters')
    ->add(Field::text('author', [
        'rules' => 'required|min:3',
        'messages' => [
            'required' => 'Required, the :attribute field is.',
            'min' => [
                'string' => 'At least :min characters, the :attribute must be.'
            ]
        ]
    ]))
    ->set();
```

The `:attribute` and `:min` are validation placeholders terms. In order to know what type of messages you can define per rule, follow default messages defined into the `validation.php` file.

### Customize validation attribute

On validation, the `:attribute` placeholder term is replaced by the field name (without the prefix). If you want to provide an attribute name different than the field name, you may pass a `placeholder` property to the field like so:

```php
Metabox::make('settings', 'Parameters')
    ->add(Field::text('author', [
        'rules' => 'required',
        'placeholder' => 'guy'
    ]))
    ->set();
```

The above example will display the following error message:

```html
The guy field is required.
```

Localization
------------

When using the metabox with custom fields, the user interface is displaying texts regarding its current state. Each label can be translated. A translation is associated to a key name. Here is the list of default labels applied to a metabox:

```php
[
    'done' => __('Saved'),
    'error' => __('Saved with errors'),
    'saving' => __('Saving'),
    'submit' => sprintf('%s %s', __('Save'), $metabox->getTitle())
]
```

You can override the default labels applied to a metabox by calling the `addTranslation` method and pass it the key name and the translation as a value:

```php
Metabox::make('properties', 'post')
    ->add(Field::text('author'))
    ->addTranslation('done', 'Success!')
    ->set();
```

### Get metabox translation

You can add as many translations strings as you want to your metabox. Especially when building a custom user interface with it.

A metabox instance provides methods to retrieve translations and use them in your interface. You can use the `getTranslations` method to get the full list of translations or you can call the `getTranslation` method to grab a specific one:

```php
$box = Metabox::make('custom', 'Options')
    ->addTranslation('title', __('A title'))
    ->addTranslation('subtitle', __('The subtitle'))
    ->set();
    
$all = $box->getTranslations();

$title = $box->getTranslation('title');
```

> You will rarely need to grab a metabox translation out of it. Those methods are useful when you create a custom metabox user interface and want to get optional translation in your interface/view.

Retrieve data
-------------

In order to retrieve the custom fields data, you can use the core `get_post_meta` function. Here is an example based on an `author` custom text field:

```php
$author = get_post_meta($post_id, 'th_author', true);
```

> Please note that custom fields meta key names are now using a [prefix](#prefixing) by default.

Customize the metabox
---------------------

If you want to create a custom user interface, you can do so by passing a callback to your metabox using the `setCallback` method:

```php
Metabox::make('custom', 'post')
    ->setCallback(function () {
        return view('metabox.custom');
    })
    ->set();
```

In the above example, the metabox is displaying the `metabox.custom` view. From a metabox callback you can return a `Renderable` instance (view), a string or even a `Response` instance.

### Class callback

The metabox API can also handle a class callback using the `ClassName@method` syntax:

```php
Metabox::make('custom', 'post')
    ->setCallback('App\Metabox\Custom@show')
    ->set();
```

Just like in action or filter, you have to provide the full class name, with its namespace, for the callback.

### Callback arguments

By default, the metabox callback receives as a parameter an `$args` array containing an instance of your metabox, an instance of the current post and the screen it is attached to:

```php
Metabox::make('custom', 'post')
    ->setCallback(function ($args) {
        return view('metabox.custom', [
            'post' => $args['post'],
            'metabox' => $args['metabox'],
            'screen' => $args['screen']
        ]);
    })
    ->set();
```

You can also provide additional data to your metabox by using the `setArguments` method and pass it an array of key value pairs:

```php
Metabox::make('custom', 'post')
    ->setCallback('App\Metabox\Custom@show')
    ->setArguments([
        'users' => App\User::all()
    ])
    ->set();
````

Those arguments are then provided to your callback:

```php
<?php
namespace App\Metabox;
    
class Custom
{
    public function show($args)
    {
        return view('metabox.custom', [
            'users' => $args['users']
        ]);
    }
}    
```

Display based on capability
---------------------------

You can configure your metabox to display based on current user capability. In order to do so, you may use the `setCapability` method:

```php
Metabox::make('custom', ['post', 'page'])
    ->setCapability('edit_posts')
    ->set();
```

Display based on template
-------------------------

You can limit metabox display to WordPress templates by calling the `setTemplate` method:

```php
Metabox::make('custom', ['post', 'page'])
    ->setTemplate(['featured', 'holidays'], 'post')
    ->setTemplate('team')
    ->set();
```

The `setTemplate` method accepts as a first argument a template name or an array of templates and as a second argument the screen|post type to which the templates are associated to. By default, the screen parameter is set to `page` screen.

> You can chain the `setTemplate` method as many times as you want.


Next
----

* [Taxonomy guide]({{url}}/taxonomy)
* [Page guide]({{url}}/page)
