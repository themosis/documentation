Form
====

1. Opening a form
2. Labels
3. Text, Email, Textarea, Hidden inputs
4. Checkbox and radio inputs
5. Select field
6. Buttons
7. Miscellaneous inputs

1.Opening a form
----------------

```php
{{ Form::open() }}

	// Form inputs

{{ Form::close() }}
```

By default, the form sets the method attribute to `POST` and its action attribute to the current URL.

The `open()` method return the opening form HTML tag and the `close()` method return the closing form tag.

> When using the `Form::open()` method, it automatically adds WordPress nonce and http referer hidden fields into your form. The nonce field uses the `Session::nonceName` and `Session::nonceAction` constants to populate its value.

### Set a custom action attribute

In order to set a custom action attribute, simply fill in the first parameter of the open form method:

```php
// This will output <form action="http://www.my-website.com/register/">
{{ Form::open(home_url('/register/')) }}

	// Form inputs

{{ Form::close() }}
```

### Change the form method attribute

Specify the second parameter of the open() method. Only `get` and `post` values are accepted.

```php
{{ Form::open('', 'get') }}

	// Form inputs

{{ Form::close() }}
```
> Set the first parameter as `empty string` of `null` will set the action attribute to the current URL.

### Make sure to submit your data to a SSL page

By setting the third parameter of the `open()` method to `true`, the action attribute will change its protocol to `https`.

```php
{{ Form::open('contact', 'post', true) }}

	// Form inputs

{{ Form::close() }}
```

### Add custom attributes to the opening <form> tag

You can pass as a fourth parameter an array of key => value in order to add custom attribute. The `key` is your attribute name and the `value` is the attribute value.

```php
{{ Form::open(null, 'post', false, array(
	'data-message' 	=> 'The form message.',
	'enctype'		=> 'multipart/form-data',
	'id'			=> 'register-form'
)) }}

	// Form inputs

{{ Form::close() }}
```

2.Labels
--------

Output a label tag:

```php
// This will output <label for="my-input">Display text</label>
{{ Form::label('my-input', 'Display text') }}
```

Add extra attributes to a label:
```php
{{ Form::label('my-input', 'Display text', array(
	'class'		=> 'super'
)) }}
```

3.Text, Email, Textarea, Hidden inputs
--------------------------------------

Output a text input:

```php
// This will output <input type="text" name="name">
{{ Form::text('name') }}
```

Specify a default value:

```php
{{ Form::text('name', 'The default name value') }}
```

Add custom attributes:

```php
{{ Form::text('name', 'Default value', array(
	'class'			=> 'super',
	'placeholder'	=> 'Insert your name here'
)) }}
```

> The `Form::email()`, `Form::hidden()` and `Form::textarea()` methods use the same signature.

4.Checkbox and radio inputs
---------------------------

Output one checkbox:

```php
// This will output <input type="checkbox" name="toggle">
{{ Form::checkbox('toggle') }}
```

> When checking for submitted data, you only have to check by default if a value is equal to `on` or `off`. If you submit a value different than `on` or `off`, you have to look for the `checked` attribute by passing it as an extra attribute.

One checkbox default value:

```php
{{ Form::checkbox('toggle', 'on') }}
```

Add custom attributes:

```php
{{ Form::checkbox('toggle', 'enabled', array(
	'checked'	=> 'checked',
	'class'		=> 'super'
)) }}
```

### Multiple checkboxes and radio

In order to specify multiple checkboxes, you will use the `Form::checkboxes` method like so:

```php
{{ Form::checkboxes('colors', array(
	'red',
	'green',
	'blue'
)) }}
```

> The first parameter is the common name of your checkboxes. The second parameter is an array of values/choices.

Pass default "checked" values:

```php
{{ Form::checkboxes('colors', array(
	'red',
	'green',
	'blue'
), array(
	'green'
)) }}
```

Add extra attributes:

```php
{{ Form::checkboxes('colors', array(
	'red', 
	'green',
	'blue'
), array(), array(
	'class'		=> 'super'
)) }}
```

> The `Form::radio()` method uses the same signature as `Form::checkboxes()`

Output a radio input:

```php
{{ Form::radio('gender', array(
	'woman',
	'man'
)) }}
```
5.Select field
--------------

Generate a select tag with indexed options values:

```php
{{ Form::select('country', array(
	array(
		'belgium',
		'france',
		'usa'
	)
)) }}
```

This will output the following `<select>` tag:

```html
<select name="country">
	<option value="0">Belgium</option>
	<option value="1">France</option>
	<option value="2">Usa</option>
</select>
```

Generate a select tag with custom options values:

```php
{{ Form::select('country', array(
	array(
		'be'	=> 'belgium',
		'fr'	=> 'france',
		'us'	=> 'usa'
	)
)) }}
```

This will output the following `<select>` tag:

```html
<select name="country">
	<option value="be">Belgium</option>
	<option value="fr">France</option>
	<option value="us">Usa</option>
</select>
```

Generate a select tag with grouped options values:

```php
{{ Form::select('country', array(
	'Europe'	=> array(
		'belgium',
		'france'
	),
	'America'	=> array(
		'usa'
	)
)) }}
```

The code above will output `<optgroup>` tags with indexed options values:

```html
<select name="country">
	<optgroup label="Europe">
		<option value="0">Belgium</option>
		<option value="1">France</option>
	</optgroup>
	<optgroup label="America">
		<option value="2">Usa</option>
	</optgroup>
</select>
```

Same select tag but with custom options values:

```php
{{ Form::select('country', array(
	'Europe'	=> array(
		'be'	=> 'belgium',
		'fr'	=> 'france'
	),
	'America'	=> array(
		'us'	=> 'usa'
	)
)) }}
```

### Pass default value(s)

You can pass a default value by filling in the third parameter of the `select()` method:

```php
{{ Form::select('country', array(
	array(
		'belgium',
		'france',
		'usa'
	)
), 0) }}
```
> This set the default value to indexed value `0` (belgium). For custom options, simply pass the custom value.
> When using multiple selection, pass in an array as default values.

### Add custom attributes

Like other form fields, pass an array as the fourth parameter for custom attributes.

```php
{{ Form::select('countries', array(
	array(
		'belgium',
		'france',
		'usa'
	)
), null, array(
	'class'		=> 'super',
	'multiple'	=> 'multiple'
)) }}
```

6.Buttons
---------

Generate a button tag:

```php
{{ Form::button('toggle', 'Click me!') }}
```

This will output the following html:

```html
<button type="button" name="toggle">Click me!</button>
```

Generate a submit button:

```php
{{ Form::submit('register', 'Sign up') }}
```

This will output the following html:

```html
<button type="submit">Sign up</button>
```

Add custom attributes by passing an array as a third parameter:

```php
{{ Form::button('toggle', 'Click me!', array('class' => 'super')) }}
{{ Form::submit('register', 'Sign up', array('id' => 'awesome')) }}
```

7.Miscellaneous inputs
----------------------

The `Form` class provides an `input()` method in order to create any type of input tags.

```php
Form::input($type, $name, $value = null, array $attributes = array())
```
- **$type**: _string_ The input type
- **$name**: _string_ The input name attribute
- **$value**: _mixed_ A default or registered value
- **$attributes**: _array_ Extra attributes

Generate a text input:

```php
{{ Form::input('text', 'your-name') }}
```

Generate a password input:

```php
{{ Form::input('password', 'your-password') }}
```

Generate a color input:

```php
{{ Form::input('color', 'my-color') }}
```

Other ressources:
-----------------

Check the validation and input guide for documentation on how to retrieve and validate submitted data.

* [Validation and input](http://framework.themosis.com/docs/validation/)