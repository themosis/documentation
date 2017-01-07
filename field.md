Field
=====

The Themosis framework is bundled with an API to build WordPress custom fields. The framework gives you a list of predefined fields to use that leverage WordPress core API in order to customize your content.

The framework handles the display of your custom fields in the WordPress administration.

The `Field` class is an helper class in order to build custom fields in WordPress. It's a generic class that you should use to build any type of custom fields.

This class is used for:

* Adding custom fields to a metabox
* Adding custom fields to an option page (Settings API)
* Adding custom fields to users
* Adding custom fields to taxonomy terms
* More to come (Customize,...)

The `Field` class implements the `Form` class to render its inputs but also provides a UI regarding the context: metabox, settings, user, terms,...

Here is the list of custom fields available:

* Checkbox
* Collection
* Color
* Date
* Editor
* Infinite
* Media
* Number
* Password
* Radio
* Select
* Text
* Textarea

### Text field

Build a simple text field: `<input type="text"/>`

```php
Field::text($name, $features = [], $attributes = []);
```

* **$name**: _string_ The field/meta name/key.
* **$features**: _array_ Features of the custom field.
* **$attributes**: _array_ A list of attributes to add to the input field.

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
