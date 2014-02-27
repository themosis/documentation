Metabox
=======

1. Field
2. Metabox
3. Meta

Themosis framework comes bundled with APIs to build WordPress metabox and custom fields. The framework gives you a list of predefined fields to use in order to customize your content. The framework handles the display of the fields in the WordPress administration.

Before using the `Metabox` class, you first need to define the fields you want to insert into it.

1. Field
========

The `Field` class is an helper class in order to build custom fields in WordPress. It's a generic class that you should use to build any type of custom fields.

This class is used for:

* Adding custom fields to a metabox
* Adding custom fields to an option page
* Adding custom fields to a taxonomy
* More to come (Users, ...)

> **Note:** At the moment, all fields are available for metabox. We're still implementing them to the other categories.

Here is the list of custom fields available:

* Text
* Textarea
* Checkbox
* Checkboxes
* Radio
* Select
* Editor
* Media
* Infinite

### Text field

Build a simple text field: `<input type="text"/>`

```php
Field::text($name, $extra = array());
```

* `$name`: _string_. The field/meta name/key.
* `$extras`: _array_. Extra parameters to add to the field.

Possible values for the `$extras` parameter:

```php
$extras = array(

	'title' 	=> 'The field display title. By default it uses the $name.',
	'info'		=> 'Add a helper text/description to the field.',
	'default' 	=> 'You can define a default value for the field.',
	'class'		=> 'Use to filter the value during sanitization before saving the data - In progress',
	'section'	=> 'Specific to option page. Name of a registered section. Attach the field to the section.'

);
```

### Textarea field

Build a textarea field: `<textarea></textarea>`

```php
Field::textarea($name, $extras = array());
```

* `$name`: _string_. The field name.
* `$extras`: _array_. The extras properties. Check the Text field.

### Checkbox field

Build a **single** checkbox field: `<input type="checkbox"/>`

```php
Field::checkbox($name, $extras = array());
```

* `$name`: _string_. The field name.
* `$extras`: _array_. The extra properties. Check the Text field.

### Checkboxes field

Build a **group** of checkbox fields.

```php
Field::checkboxes($name, $options, $extras = array());
```

* `$name`: _string_. The field name.
* `$options`: _array_. An array of values. It doesn't work with associative array. The value is used as a label.
* `$extras`: _array_. The extra properties. Check the Text field.

### Radio field

Build radio fields: `<input type="radio" />`

```php
Field::radio($name, $options, $extras = array());
```

* `$name`: _string_. The field name.
* `$options`: _array_. An array of values. It doesn't work with associative array. The value is used as a label.
* `$extras`: _array_. The extra properties. Check the Text field.

### Select field

Build select field: `<select></select>`

The select field is highly customizable. You can build simple select tag or with subgroups or/and define if you need multiple selections.

You can also define the value of your option tag to be numeric or custom: `<option value="$key">$value</option>`

```php
Field::select($name, $options, $multiple = false, $extras = array());
```

* `$name`: _string_. The field name.
* `$options`: _array_. The select options values.
* `$multiple`: _boolean_. If select tag use single or multiple value. Default to `false`.
* `$extras`: _array_. The extra properties. Check the Text field.