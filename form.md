Form
====

1. Opening a form
2. Labels
3. Text, Email, Textarea, Hidden inputs
4. Checkbox and radio inputs
5. Select field
6. Buttons and submit input
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



