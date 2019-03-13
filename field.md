Field
=====

- [Introduction](#introduction)
- [Shared options](#shared-options)
- [Fields](#fields)
    - [Text field](#text-field)
    - [Textarea field](#textarea-field)

> The field documentation is not yet up to date with release 2.0.

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



Text field
------------

The text field output an input tag with a type of `text`.

```php
Field::text($name, $options = []);
```

* **$name**: _string_ The field name.
* **$options**: _array_ Features of the custom field.

Possible values for the `$features` parameter:

```php
$features = [
    'title'   => 'The field display title. By default it uses the $name.',
    'info'    => 'Add a helper text/description to the field.',
    'default' => 'You can define a default value for the field.'
];
```

#### Note about the fields attributes parameter

Some fields implement a parameter called `$attributes`. This parameter allows you to specify custom attributes to the field HTML tag.

For example, let's add a custom class to a text field:

```php
Field::text('author', ['title' => 'Book author'], ['class' => 'custom-text-class']);
```

You can add any attributes you want each time you see a field implementing the `$attributes` parameter.

### Textarea field

Build a textarea field: `<textarea></textarea>`

```php
Field::textarea($name, $features = [], $attributes = []);
```

* **$name**: _string_ The field name.
* **$features**: _array_ Field features.
* **$attributes**: _array_ Field attributes.

### Number field

Build a simple number field: `<input type="number"/>`

```php
Field::number($name, $features = [], $attributes = []);
```

* **$name**: _string_ The field name.
* **$features**: _array_ Field features.
* **$attributes**: _array_ Field attributes.

### Date field

Build a simple date field: `<input type="date"/>`

```php
Field::date($name, $features = [], $attributes = []);
```

* **$name**: _string_ The field name.
* **$features**: _array_ Field features.
* **$attributes**: _array_ Field attributes.

### Password field

Build a simple password field: `<input type="password"/>`

```php
Field::password($name, $features = [], $attributes = []);
```

* **$name**: _string_ The field name.
* **$features**: _array_ Field features.
* **$attributes**: _array_ Field attributes.

### Color field

Build a color field and display a color picker widget so users can pick a color easily.

```php
Field::color($name, $features = [], $attributes = []);
```

* **$name**: _string_ The field name.
* **$features**: _array_ Field features.
* **$attributes**: _array_ Field attributes.

### Checkbox field

Build a **single** or **multiple** checkbox field: `<input type="checkbox"/>`

```php
Field::checkbox($name, $options, $features = [], $attributes = []);
```

* **$name**: _string_ The field name.
* **$options**: _string|array_ One or more options to check.
* **$features**: _array_ Field features.
* **$attributes**: _array_ Field attributes. If multiple checkbox, the same attributes will be added to each checkbox.

#### Create a single checkbox

In the example below, let's define a checkbox with one option. This might be a toggle option:

```php
Field::checkbox('toggle', 'activate');
```

The above code will output a checkbox field with displayed label of `Activate` and a value of `activate`.

If you want to customize the text displayed beside the checkbox, set a `key/value` pair for your checkbox options like so:

```php
Field::checkbox('toggle', ['activate' => 'Activate this option']);
```

The code will output the checkbox like the previous example except that its text beside the checkbox is now customized.

##### Add attributes to the checkbox label

The checkbox field renders a label tag beside each of its inputs. You can now pass custom attributes to those label tags by passing a `label` attribute to the field attributes property:

```php
Field::checkbox('toggle', 'activate', [], ['label' => ['class' => 'label-class']]);
```

Pass an array of attributes to the `label` special property of the checkbox field.

#### Create a multi-choices checkbox

Simply pass an array of choices/options to your checkbox field like so:

```php
Field::checkbox('colors', ['red', 'green', 'blue']);
```

### Radio field

Build radio field: `<input type="radio" />`

The radio field also provides multiple radio inputs with a label beside each input. The `label` attribute is also available to the radio field and you can also customize the displayed text for each option/choice of your field.

```php
Field::radio($name, $options, $features = [], $attributes = []);
```

* **$name**: _string_ The field name.
* **$options**: _array_ A list of options.
* **$features**: _array_ Field features.
* **$attributes**: _array_ Field attributes. The `label` attribute is also available. Read the checkbox field documentation above on how to use it.

### Select field

Build a select field: `<select></select>`

The select field is highly customizable. You can build simple select tag or with subgroups or/and define if you need multiple selection.

You can also define the value of your option tag to be numeric or custom: `<option value="$key">$value</option>`

```php
Field::select($name, $options, $features = [], $attributes = []);
```

* **$name**: _string_ The field name.
* **$options**: _array_ The select field options.
* **$features**: _array_ Field features.
* **$attributes**: _array_ Field attributes.

> Note: If you want to make multi selection field, simply add the `multiple` attribute. Previous release proposed a `$multiple` argument which is deprecated in favor of the `$attributes['multiple']` parameter.

#### Basic select field

Build a simple select field with **numeric** values:

```php
Field::select('my-field', [
    [
        'None',
        'Belgium',
        'France',
        'United States'
    ]
], ['title' => 'Choose a country:']);
```

Build a simple select field with **custom** values:

```php
Field::select('my-field', [
    [
        'none' => 'None',
        'bel'  => 'Belgium',
        'fra'  => 'France',
        'usa'  => 'United States'
    ]
], ['title' => 'Choose a country:']);
```

#### Select field with subgroups

Build a select field with subgroup of options using **numeric** values:

```php
Field::select('my-field', [
    'europe' => [
        'Belgium',
        'France'
    ],
    'america' => [
        'United States'
    ]
]);
```

Build a select field with subgroup of options using **custom** values:

```php
Field::select('my-field', [
    'europe' => [
        'bel' => 'Belgium',
        'fra' => 'France'
    ],
    'america' => [
        'usa' => 'United States'
    ]
]);
```

#### Select multiple values

Simply set the multiple attribute of the select field like so:

```php
Field::select('my-field', [
    [
        'None',
        'Belgium',
        'France',
        'United States'
    ]
], ['title' => 'Choose a country:'], ['multiple']);
```

### Editor field

Build a WordPress Editor TinyMCE field.

**Note:** This field isn't actually working with the `Infinite` custom field.

```php
Field::editor($name, $features, $settings);
```

* **$name**: _string_ The field name.
* **$features**: _array_ Field features.
* **$settings**: _array_ An array of parameters of the WordPress editor. Use this [codex guide](https://codex.wordpress.org/Function_Reference/wp_editor) to define your editor **settings**.

### Media field

Build a media field. This field allows you to upload or attach any file by using the WordPress Media API.

```php
Field::media($name, $features = []);
```

* **$name**: _string_ The field name.
* **$features**: _array_ Field features.

By default, the media field allows you to attach images only.

```php
Field::media('profile');
```

In order to allow the media field to attach other files except images, you need to specify the `type` feature like so:

```php
Field::media('report', [
    'title' => 'Attach report',
    'type'  => 'application'
])
```

In some scenario, you might need to define multiple `types`, like having the ability to let users choose a PDF file or an image. In order to define multiple `types`, simply pass an array:

```php
Field::media('attachments', [
    'title' => 'Share a file',
    'type' => ['image', 'application']
]);
```

> The `type` feature only accepts 5 values: `image`, `text`, `application`, `video` and `audio`.

### Collection field

Allows a user to add/edit/delete a collection of media files. The collection field UI allows an end-user to add, order, delete or bulk delete files. Useful for galleries or specifying a list of files to download,...

> Current implementation of the collection field only saves its meta data as one single serialized value.

```php
Field::collection($name, $features = []);
```
- **$name**: _string_ The field name.
- **$features**: _array_ Field features. Same as the media field with an extra `limit` property which allows you to limit the number of media a user can add to the collection.

#### Create a basic collection of images

```php
// The default collection field allows you to manage images.
Field::collection('gallery');
```

#### Create a collection of files

```php
// Same but only for files.
Field::collection('papers', ['type' => 'application']);
```

#### Create a collection of images and files

```php
Field::collection('files', ['type' => ['image', 'application']]);
```

#### Limit the number of media

Using the `limit` feature property, you can limit the number of media files a user can add to the collection field:

```php
// Limit the number of media to add to a collection.
Field::collection('pics', ['limit' => 5]);
```
> Allowed `type` values: `image`, `text`, `application`, `video`, `audio`.

### Infinite field

Build a repeatable field. This allows you to define a single field or a group of fields to be repeated.

> Current implementation of the infinite field only saves its meta data as one single serialized value.

```php
Field::infinite($name, $fields, $features);
```

* **$name**: _string_ The field name.
* **$fields**: _array_ An array of fields to repeat. Use any methods of the `Field` class excepts the `editor` field.
* **$features**: _array_ Field features.

> Please note that Infinite fields can't be nested currently.

Example of an infinite field:

```php
Field::infinite('books', [
    Field::text('title'),
    Field::textarea('excerpt'),
    Field::media('cover-image')
]);
```

You can limit the number of repeatable rows by setting the `limit` property like so:

```php
Field::infinite('books', [
    Field::text('title'),
    Field::media('image')
], [
    'title' => 'List of books',
    'limit' => 10
])
```

The above code has a limit set to 10 rows of books.

Next
----
Check those guide to implement your custom fields:

* [Metabox guide]({{url}}/metabox)
* [Page guide]({{url}}/page)
* [User guide]({{url}}/user)
* [Taxonomy guide]({{url}}/taxonomy)
