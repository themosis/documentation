Field
=====

- [Introduction](#introduction)
- [Shared options](#shared-options)
- [Fields](#fields)
- [General usage](#general-usage)
- [Retrieve the field name](#retrieve-the-field-name)
- [Change the field prefix](#change-the-field-prefix)
- [Field options](#field-options)
- [Field value](#field-value)
- [Field view](#field-view)
- [Field rendering](#field-rendering)
- [Field as a resource](#field-as-a-resource)

Introduction
------------

The Themosis framework is bundled with an API to build custom fields. This API is leveraged by the framework to help you build WordPress metabox custom fields, page settings, ... and form fields.

The `Field` class is a generic class that you should use to build any type of custom fields within your application. This class is used for:

* Adding fields to a metabox
* Adding fields to an option page (Settings API)
* Adding fields to a form
* And more...

The `Field` class has been fully refactored and no longer returns just a HTML tag. Each time a field is created, it returns a `FieldTypeInterface` instance. Depending on the context, the field API has methods to return each instance either as a string for HTML output, as an array of properties or as a JSON object.

Each field method signature follow the same structure, the first argument is the `name` and the second argument is an array of `options` like so:

```php
Field::text('name', [
    'label' => 'Full name',
    ...
]);
```

Before going into each field and their specific options, let's first list all the shared options you can use on all instances.

Shared options
--------------

<div class="options-list" markdown="1">
[attributes](#attributes)
[data](#data)
[data_type](#data-type)
[errors](#errors)
[flush](#flush)
[group](#group)
[info](#info)
[l10n](#l10n)
[label](#label)
[label_attr](#label-attr)
[mapped](#mapped)
[messages](#messages)
[placeholder](#placeholder)
[rules](#rules)
[show_in_rest](#show-in-rest)
[theme](#theme)
</div>

#### attributes

The `attributes` option allows you to define an array of HTML attributes for your field. The key is the attribute name and its value the attribute value. For some attributes, it is also possible to omit the value and only pass the key name.

```php
Field::text('demo', [
    'attributes' => [
        'id' => 'demo-id',
        'class' => 'custom-demo',
        'required'
    ]
]);
```

#### data

The `data` option lets you define a default value for your field. You can either set a string or an array default value depending on the field type.

```php
Field::text('demo', [
    'data' => 'Initial value'
]);
```

#### data_type

The `data_type` option is mainly used for fields combined with WordPress elements like metabox, settings, ... The `data_type` value is used by the WordPress RestAPI and let you define the type of the data stored by the field: `string`, `boolean`, `array`, ... and that is going to be exposed by the RestAPI schema.

```php
Field::text('demo', [
    'data_type' => 'string'
]);
```

#### errors

The `errors` option lets you display or not the field errors messages. The option only accepts a boolean value. Default value is `true`.

> The `errors` option is not handled by the field when used with a metabox.

```php
Field::text('demo', [
    'errors' => false
]);
```

#### flush

The `flush` option defines if the value of the field should be flushed after a successful validation. By default, it is set to `false`.

> Note that if the field is used from the context of a form, by default it is set to `true`.

```php
Field::text('demo', [
    'flush' => true
]);
```

#### group

The `group` option lets you organize your fields by groups. Mainly used by the default form API where you can specify a group name for your field. The parent container is then using that information to group fields and wrap them in a HTML container at output.

Here is an example of its usage with a form:

```php
return $factory->make()
    ->add($fields->text('firstname', [
        'group' => 'personal'
    ]))
    ->add($fields->text('lastname', [
        'group' => 'personal'
    ]))
    ->add($fields->email())
    ->get();
```

In the above example, the fields `firstname` and `lastname` will be grouped together at render time.

#### info

The `info` option lets you specify an extra information text or content generally displayed right under the field.

```php
Field::text('demo', [
    'info' => 'This is a short description displayed below the field.'
]);
```

#### l10n

The `l10n` option lets you store translations texts for your field. You can pass an array of key/value pairs where the key is the translation ID and the value, the translation text. This option is helpful when you need to provide translated text on fields rendered through a JavaScript component.

```php
Field::text('demo', [
    'l10n' => [
        'hello' => 'World',
        'say' => __('something', 'textdomain')
    ]
]);
```

#### label

The `label` option lets you specify the label HTML element text rendered beside the field.

```php
Field::text('demo', [
    'label' => 'Demonstration'
]);
```

#### label_attr

The `label_attr` option lets you define the HTML attributes of the field label element. You can pass an array of key/value pairs where the key is the attribute name and the value, the attribute value.

```php
Field::text('demo', [
    'label_attr' => [
        'class' => 'form-label'
    ]
]);
```

#### mapped

The `mapped` option is used by the form API in order to know if the field should be mapped or not to a data object property. By default, its value is set to `true`.

Here is an example on a form context:

```php
return $factory->make($dto)
    ->add($fields->password('password'))
    ->add($fields->password('password_confirmation', [
        'mapped' => false
    ]))
    ->get();
```

In the above example, the field `password_confirmation` is not mapped to the given data object.

#### messages

The `messages` option lets you define a list of validation messages by rule. You can pass an array of key/value pairs where the key is the validation rule name and the value, the validation message text.

```php
Field::text('demo', [
    'rules' => 'required|min:6',
    'messages' => [
        'required' => 'The :attribute field is a required and very important field.',
        'min' => 'The :attribute field must be at least :min characters long.'
    ]
]);
```

#### placeholder

The `placeholder` option is used by the validation. You can specify a custom text value for the `: attribute` placeholder in a validation message. By default it takes the field name as a value.

```php
Field::text('demo', [
    'placeholder' => 'demonstration'
]);
```

#### rules

The `rules` option lets you define a list of validation rules for the field. Validation is handled by the `illuminate/validation` package. [Click here](https://laravel.com/docs/validation#available-validation-rules) to view a full list of available validation rules.

```php
Field::text('demo', [
    'rules' => 'required|min:6|max:191'
]);
```

#### show_in_rest

The `show_in_rest` option is used by WordPress. The option accepts a boolean value and lets you specify if your field (post meta, ...) should be shown in the WordPress Rest API. This option mainly works within the context of a metabox, a page setting, a term meta, ... Its default value is set to `false`

Here is an example in the context of a metabox custom field:

```php
Metabox::make('properties', 'post')
    ->add(Field::text('show_me', [
        'show_in_rest' => true
    ]))
    ->set();
```

#### theme

The `theme` option lets you choose the output style of your field. This option only works within the context of a form. You can choose between the default `themosis` theme or the `bootstrap` theme.

> Generally, it is not necessary to define a theme value on a per field basis. The form API also provide a theme option in order to apply it to all its children elements (fields).

```php
Field::text('demo', [
    'theme' => 'bootstrap'
]);
```

Fields
------

Here is the list of fields bundled with the framework. Please note that some fields from the list below are not available on some contexts.

<div class="options-list" markdown="1">
[Button](#button-field)
[Checkbox](#checkbox-field)
[Choice](#choice-field)
[Collection](#collection-field)
[Color](#color-field)
[Editor](#editor-field)
[Email](#email-field)
[Hidden](#hidden-field)
[Integer](#integer-field)
[Media](#media-field)
[Number](#number-field)
[Password](#password-field)
[Submit](#submit-field)
[Text](#text-field)
[Textarea](#textarea-field)
</div>

### Button field

The button field can only be used within a form context in order to add a submit/button field.

```php
Field::button($name, $options = []);
```

- **$name**: _string_ The field name
- **$options**: _array_ The field options

### Checkbox field

The checkbox field only handles a **single** checkbox input. In order to build a field with multiple checkbox inputs, please use the new [choice field](#choice-field).

```php
Field::checkbox($name, $options = []);
```

- **$name**: _string_ The field name
- **$options**: _array_ The field options

### Choice field

The choice field allows you to create 4 different types of field:

1. [A single select field](#single-select-field)
2. [A multiple select field](#multiple-select-field)
3. [A radio field](#radio-field)
4. [A multiple checkbox field](#multiple-checkbox-field)

The choice field accept as a first parameter a name and in order to attach a list of options to your field, you need to pass a `choices` option to it like so:

```php
Field::choice('color', [
    'choices' => ['red', 'green', 'blue']
]);
```

The above example renders a single select input element.

#### Single select field

The choice field is by default configured to output a single select input element. You can pass a list of options by using the `choices` property.

The `choices` field option accepts an array as a value and the structure of your array also defines its output.

##### Define a select field using a numeric array

If you pass a numeric array as a value for your field `choices` option, each array item is considered as the option value.

For example:

```php
// Select field with numeric array:
Field::choice('color', [
    'choices' => [
        'red',
        'green',
        'blue'
    ]
]);

// Output the following HTML:
<select name="th_color">
    <option value="red">Red</option>
    <option value="green">Green</option>
    <option value="blue">Blue</option>
</select>
```

> The array value is always used as the option element value.

##### Define a select field using an associative array

If you pass an associative array as a value for your field `choices` option, the key is option element text and the value its value attribute.

For example:

```php
// Select field with associative array
Field::choice('color', [
    'choices' => [
        'Red Color' => 'red',
        'Green Color' => 'green',
        'Blue Color' => 'blue'
    ]
]);

// Output the following HTML:
<select name="th_color">
    <option value="red">Red Color</option>
    <option value="green">Green Color</option>
    <option value="blue">Blue Color</option>
</select>
```

##### Define a select field with options groups

If you pass an array of array as a value for your field `choices` option, you can output a select field with optgroup HTML elements.

For example:

```php
// Select field with optgroup elements
Field::choice('color', [
     'choices' => [
         'Light Colors' => [
             'red',
             'green',
             'blue'
         ],
         'Dark Colors' => [
             'Dark purple' => 'purple',
             'Dark brown' => 'brown',
             'Deep black' => 'black'
         ]
    ]
]);

// Output the following HTML
<select name="th_color">
    <optgroup label="Light Colors">
        <option value="red">Red</option>
        <option value="green">Green</option>
        <option value="blue">Blue</option>
    </optgroup>
    <optgroup label="Dark Colors">
        <option value="purple">Dark purple</option>
        <option value="brown">Dark brown</option>
        <option value="black">Deep black</option>
    </optgroup>
</select>
```

#### Multiple select field

The choice field can also be used to build a select field with multiple options. In order to let a user select multiple values, set the field `multiple` option to `true` like so:

```php
Field::choice('color', [
    'choices' => [
        'red',
        'green',
        'blue'
    ],
    'multiple' => true
]);
```

#### Radio field

The choice field can generate a radio input field by setting the field `expanded` option to `true`:

```php
Field::choice('direction', [
    'choices' => [
        'left',
        'right'
    ],
    'expanded' => true
]);
```

#### Multiple checkbox field

You can create a multiple checkbox field by setting the `expanded` and `multiple` option of the choice field to `true`:

```php
Field::choice('languages', [
    'choices' => [
        'css',
        'html',
        'javascript,'
        'php'
    ],
    'expanded' => true,
    'multiple' => true
]);
```

### Collection field

The collection field is only available within a metabox context and allows a user to insert multiple WordPress media items. The field only stores the WordPress attachment ID. 

The collection field UI allows an end-user to add, order, delete or bulk delete attachments. Useful for galleries or specifying a list of files to download,...

> The collection field is only available within a metabox context.

```php
Field::collection($name, $options = []);
```
- **$name**: _string_ The field name
- **$options**: _array_ The field options

The collection field also accepts a `limit` option which allows you to limit the number of media a user can add to the collection.

You can also specify the `type` of media attachment to insert into the collection by passing a `type` option to the field. The `type` option accepts a string or an array of those values: `image`, `application`, `video`, `audio`, `text`. By default, the `type` options has the following value `['application', 'image']`

#### Create a basic collection of images and files

```php
// The default collection field allows you to manage images and files
Field::collection('gallery');
```

#### Create a collection of files

```php
// Same but only for files.
Field::collection('papers', [
    'type' => 'application'
]);
```

#### Create a collection of images

```php
Field::collection('files', [
    'type' => 'image'
]);
```

#### Limit the number of media

Using the `limit` option, you can limit the number of media files a user can add to the collection field:

```php
// Limit the number of media to add to a collection.
Field::collection('pictures', [
    'limit' => 5
]);
```

### Color field

Build a color field and display a color picker widget so end-users can easily pick a color.

> Note that the color field is only available within a metabox context.

```php
Field::color($name, $options = []);
```

- **$name**: _string_ The field name
- **$options**: _array_ The field options

#### Default color set

You can specify a set of default colors for the field by passing a list of colors to the `colors` field option like so:

```php
Field::color('brand', [
    'colors' => [
        [
            'name' => 'Primary Color',
            'color' => '#f78da7'
        ],
        [
            'name' => 'Secondary Color',
            'color' => '#cf2e2e'
        ]
    ]
]);
```

> Note how the list of colors is formatted, you must pass an array of array with the keys `name` and `color`.

The above example will output a color field with 2 default colors and a color picker.

#### Disable color picker

The color field provides a color picker by default to let the end-user choose a custom color. You can disable this feature and in that case limit the user to use only the pre-defined color palette.

In order to disable the color picker, set the `disableCustomColors` option to `true`:

```php
Field::color('brand', [
    'disableCustomColors' => true
]);
```

### Editor field

Create a WordPress Editor TinyMCE field.

```php
Field::editor($name, $options = []);
```

- **$name**: _string_ The field name
- **$options**: _array_ The field options

You can control the behavior of the TinyMCE editor by passing a `settings` option to the editor field. The `settings` option accepts an array of parameters. Use this [codex guide](https://codex.wordpress.org/Function_Reference/wp_editor) to define the field `settings` option.

If you're using the editor field within the context of a metabox, use the `settings_js` option instead.

### Email field

Create an email input field.

```php
Field::email($name, $options = []);
```

- **$name**: _string_ The field name
- **$options**: _array_ The field options

### Hidden field

Create a hidden input field. In the context of a page setting, the hidden field displays a disabled input, the value is then visible to end-users.

> The hidden field is not supported on term fields and user fields.

```php
Field::hidden($name, $options = []);
```

- **$name**: _string_ The field name
- **$options**: _array_ The field options

In order to specify the default value for a hidden field, use the `data` option like so:

```php
Field::hidden('apikey', [
    'data' => 'XYZ123456789'
]);
```

### Integer field

Create a number input field where its value is always formatted to an integer numeric value. The field helps reinforce the value so an end-user cannot enter a float number.

```php
Field::integer($name, $options = []);
```

- **$name**: _string_ The field name
- **$options**: _array_ The field options

### Media field

Build a WordPress media field. This field allows you to upload or attach any file by using the WordPress Media API.

> The media field is only available within a metabox context.

```php
Field::media($name, $options = []);
```

- **$name**: _string_ The field name
- **$options**: _array_ The field options

By default, the media field allows you to attach images only.

```php
Field::media('profile');
```

In order to allow the media field to attach other files except images, you need to specify the `type` feature like so:

```php
Field::media('report', [
    'label' => 'Attach report',
    'type'  => 'application'
]);
```

In some scenario, you might need to define multiple types, like having the ability to let users choose a PDF file or an image. In order to define multiple types, simply pass an array:

```php
Field::media('attachments', [
    'label' => 'Share a file',
    'type' => ['image', 'application']
]);
```

> The `type` option accepts the following values: `image`, `text`, `application`, `video` and `audio`.

### Number field

Create a number input field where float values are accepted.

```php
Field::number($name, $options = []);
```

- **$name**: _string_ The field name
- **$options**: _array_ The field options

### Password field

Build a password input field.

```php
Field::password($name, $options = []);
```

- **$name**: _string_ The field name
- **$options**: _array_ The field options

### Submit field

The submit field can only be used within a form context in order to add a submit input. Similar to the button field.

```php
Field::submit($name, $options = []);
```

- **$name**: _string_ The field name
- **$options**: _array_ The field options

### Text field

The text field output an input tag with a type of `text`.

```php
Field::text($name, $options = []);
```

- **$name**: _string_ The field name
- **$options**: _array_ The field options

### Textarea field

Build a textarea field: `<textarea></textarea>`

```php
Field::textarea($name, $options = []);
```

- **$name**: _string_ The field name
- **$options**: _array_ The field options

General usage
-------------

As mentioned in the introduction, the new Field API is no longer just returning a string of HTML but each time you create a field you get back a `FieldTypeInterface` instance.

The new API allows you to use it outside of the default core contexts and gives you several methods to control the behavior of your field as well as render it or transform it in a JSON object.

It is recommended to use the `Field` facade to create new field instances. The facade is referencing a field factory class. Each time you call a factory method, it sets up the field main dependencies and sets its options.

In order to create a new field instance, simply use the `Field` facade like so:

```php
$title = Field::text('title');
```

The `$title` variable is now a `FieldTypeInterface` instance.

By default, all field instances generate a field name value that is prefixed by `th_`. If you were to render the above field, its input name will be `th_title`.

Retrieve the field name
-----------------------

As mentioned above, by default, all field names are prefixed by `th_`. This is done on purpose in order to avoid conflict with WordPress query vars. You can retrieve the composed name by using the `getName` method on a field instance like so:

```php
$title = Field::text('title');

// Output: th_title
var_dump($title->getName());
```

### Retrieve original field name

It is also possible to retrieve the field base name, without its prefix by calling the `getBaseName` method on a field instance:

```php
$title = Field::text('title');

// Output: title
var_dump($title->getBaseName());
```

Change the field prefix
-----------------------

You can modify the default prefix of a field by using the `setPrefix` method. In the case where you no longer want to use a prefix, you can pass an empty string as a parameter:

```php
$title = Field::text('title');
$title->setPrefix('wp_');

// or remove the prefix
$title->setPrefix('');
```

### Retrieve the field prefix

The `FieldTypeInterface` also provides a method in order to return the field instance prefix. Use the `getPrefix` method to retrieve its value:

```php
$title = Field::text('title');
$prefix = $title->getPrefix();

// Output: th_
var_dump($prefix);
```

Field options
-------------

The `Field` facade allows you to pass field options as a second parameter when creating a new instance. Just note that the field factory is calling the `setOptions` method on a field instance for you as well as setting the field locale and view factory dependencies.

Some fields have specific and [shared options](#shared-options). Pass field options using the second parameter of the factory method like so:

```php
Field::text($name, $options);
```

### Retrieve all field options

You can retrieve all field options by calling the `getOptions` method. The method returns an array of key/value pairs where the key is the option name and the value, the option value.

```php
$title = Field::text('title');

$options = $title->getOptions();
```

### Retrieve a specific option

It is also possible to retrieve a single option value by using the `getOption` method like so:

```php
$title = Field::text('title');

$attributes = $title->getOption('attributes');
```

If the option is not yet set on the field instance and you're calling the `getOption` method, you can also define a default value to return as a second parameter:

```php
$title = Field::text('title');

$group = $title->getOption('group', 'general');
```

> A field instance implements the ArrayAccess interface. It is also possible to get or set field options using the array syntax.

Field value
-----------

The field instance must handle its data. Each field instance contains a data transformer class. The data transformer class must implement the `DataTransformerInterface` and provides two methods: `transform` and `reverseTransform`. The `transform` method is responsible to convert raw data to an expected data type and the `reverseTransform` method to convert it back to its raw value.

For example, the Integer field has a data transformer to reinforce the value transformation as an integer. A date field might implement a transformer that takes a date string and transform it as a DateTimeImmutable object, ...

### Set field value

In order to set the field value, you may use the `setValue` method like so:

```php
$title = Field::text('title');
$title->setValue($request->get($title->getName()));
```

The `setValue` method calls the field `transform` method and store the transformed value within the instance.

### Get field value

You can retrieve a field value by using the `getValue` method:

```php
$title = Field::text('title');
$value = $title->getValue();
```

The `getValue` method returns the currently stored value of the instance. This means that it is always the transformed value that is returned.

### Get field raw value

You can retrieve the raw value of your field by calling the `getRawValue` method. The `getRawValue` is calling the `reverseTransform` field method in order to return the initial raw value:

```php
$title = Field::text('title');
$rawValue = $title->getRawValue();
```

Field view
----------

When used outside of a core context, a field does not have a view file attached to it in order to be rendered. In order to customize the output of your field, you may use the `setView` method like so:

```php
$text = Field::text('title');
$text->setView('fields.text');
```

By default, a field instance will try to use the defined view without prefixing its path with a `theme`. The above example will try to render the field using the `fields/text.blade.php` view file.

```php
<!-- resources/views/fields/text.blade.php -->
<input type="text" name="{{ $__field->getName() }}"/>
```

### Field view variables

All field views received their field instance which is accessible under the `$__field` variable:

- **$__field**: _FieldTypeInterface_ The field instance

Here is an example of a custom text view using field methods:

```php
<label {!! $__field->attributes($__field->getOption('label_attr')) !!}>{!! $__field->getOption('label') !!}</label>
<input type="text" name="{{ $__field->getName() }}" {!! $__field->attributes($__field->getAttributes()) !!} value="{{ $__field->getRawValue() }}">

```

Note the use of the `attributes` method that accepts an array of HTML attributes.

### Default framework views

If you want to use framework existing views, you need to define at least a theme to your field. Two themes are defined by the framework, you can use the `themosis` or the `bootstrap` theme and call the `setTheme` method:

```php
$title = Field::text('title');
$title->setTheme('themosis');
```

A theme is mainly a directory name that stores field views. By setting a theme, we basically prepend the theme name in front of the field default view path. On the above example, the field view path will be: `themosis.types.text` and is looking after a `themosis/types/text.blade.php` view file for output.

Field rendering
---------------

In order to render a field, you may call the `render` method on a field instance. For example, from a controller you might define your field and pass it to a view like so:

```php
public function index()
{
    $title = Field::text('title');
    $title->setTheme('themosis');
    
    return view('page', [
        'field' => $title
    ]);
}
```

And then render the field instance by using its `render` method:

```php
{!! $field->render() !!}
```

Field as a resource
-------------------

The `FieldTypeInterface` provides methods to transform a field instance into a resource.

The field transformation uses the [PHP League Fractal](https://fractal.thephpleague.com/) package. You must add a fractal `Manager` instance to your field as well as a transformer class in order to modify the field as a resource. Use the `Themosis\Forms\Resources\Factory` class to set the transformer class for your field. The factory will try to load the appropriate transformer for you.

### Prepare the field for transformation

Each field must have an instance of a fractal `Manager` and a transformer instance:

```php
use League\Fractal\Manager;
use Themosis\Forms\Resources\Factory;

$title = Field::text('title');
// Set the fractal manager instance.
$title->setManager(new Manager());
// Set the transformer using core resource factory.
$title->setResourceTransformerFactory(new Factory());
```

#### Set a custom transformer class

You can define your own transformer class by passing the fully qualified name of your transformer to the resources factory constructor like so:

```php
$title->setResourceTransformerFactory(new Factory('\\App\\Transformers\\TitleTransfomer'));
```

### Field to array

You can transform your field as an associative array by using the `toArray` method like so:

```php
use League\Fractal\Manager;
use Themosis\Forms\Resources\Factory;

$title = Field::text('title');
$title->setManager(new Manager());
$title->setResourceTransformerFactory(new Factory());

var_dump($title->toArray());

// Output the following resource
[
    "attributes" => [
      "id" => "th_title_field"
    ]
    "basename" => "title"
    "component" => "themosis.fields.text"
    "data_type" => ""
    "default" => ""
    "name" => "th_title"
    "options" => [
      "group" => "default"
      "info" => ""
      "l10n" => []
    ]
    "label" => [
      "inner" => "Title"
      "attributes" => [
        "for" => "th_title_field"
      ]
    ]
    "theme" => ""
    "type" => "text"
    "validation" => [
      "errors" => true
      "messages" => []
      "placeholder" => "title"
      "rules" => ""
    ]
    "value" => ""
]
```

### Field to JSON

You can transform your field as a JSON object by using the `toJson` method like so:

```php
use League\Fractal\Manager;
use Themosis\Forms\Resources\Factory;

$title = Field::text('title');
$title->setManager(new Manager());
$title->setResourceTransformerFactory(new Factory());

var_dump($title->toJson());
```

Next
----
Check those guide to implement your custom fields:

- [Form guide]({{url}}/form)
- [Metabox guide]({{url}}/metabox)
- [Page guide]({{url}}/page)
- [User guide]({{url}}/user)
- [Taxonomy guide]({{url}}/taxonomy)
