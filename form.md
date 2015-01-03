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

**Form::open($action = null, $method = 'POST', $ssl = false, $attributes = array()):**

- **$action**: _string_ The action attribute for the opening form tag.
- **$method**: _string_ The method attribute. Default to 'post'.
- **$ssl**: _boolean_ Tell the form to change the method protocol to `https` if using ssl. Default to false.
- **$attributes**: _array_ Extra attributes and properties for the form. 

By default, the form sets the method attribute to `POST` and its action attribute to the current URL.

The `open()` method return the opening form HTML tag and the `close()` method return the closing form tag.

> When using the `Form::open()` method, it automatically adds WordPress nonce and http referer hidden fields into your form. The nonce field uses the `Session::nonceName` and `Session::nonceAction` constants to populate its value. Check below on how to check for nonce values when form is submitted.

#### Define custom nonce values.

You can overwrite the default custom nonce fields used inside the `Form::open()` method.

In order to add your custom nonce, simply add them to the `$attributes` argument of the `Form::open()` method like so:

```php
{{ Form::open('', 'post', false, array(
	'nonce'			=> 'action',
	'nonce_action'	=> 'edit-something'
)) }}
	// Inputs
{{ Form::close() }}
```
> Make sure to use the `nonce` key for the nonce field _name_ and the `nonce_action` key for its _value_.

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

### Add custom attributes to the opening form tag

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
### Check nonce values when a form is submitted

When you use the `Form::open()` method, it automatically output an opening form tag populated with WordPress nonce values. It is considered "best-practice" to check for those nonce values when your form is submitted before proceeding to some other actions.

If you check your page source code, you'll find two hidden fields:

- The first one is a NONCE value
- The second one is the HTTP REFERER value

As explained in the `open()` method, it uses two class's constants: `Session::nonceName` and `Session::nonceAction`.

Let's verify the nonce value regarding those two constants:

```php
// A form is submitted using the POST method.
if (1 === wp_verify_nonce($_POST[Session::nonceName], Session::nonceAction))
{
	// Proceed with data
}
```
> The `nonceName` value is the nonce field name attribute. The `nonceAction` value is the nonce action string. Check `wp_verify_nonce()` function in the [codex](http://codex.wordpress.org/Function_Reference/wp_verify_nonce).

The example above is working in a front-end example. If you have a custom form on a custom page in the admin, check also the `check_admin_referer` function in the [codex](http://codex.wordpress.org/Function_Reference/check_admin_referer)

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

> The Form::checkboxes() method is deprecated. Use the Form::checkbox() method for single or multiple checkboxes.

Output one checkbox:

```php
// This will output <input type="checkbox" name="toggle[]" value="toggle">
{{ Form::checkbox('toggle', 'toggle') }}
```

> When checking for submitted data, the checkbox method registered an array of values even for one checkbox.

Add custom attributes:

```php
{{ Form::checkbox('toggle', 'enabled', array(), array(
	'class'		=> 'super'
)) }}
```

### Multiple checkboxes and radio

In order to specify multiple checkboxes, simply pass an array of choices like so:

```php
{{ Form::checkbox('colors', array(
	'red',
	'green',
	'blue'
)) }}
```

> The first parameter is the common name of your checkboxes. The second parameter is an array of choices.

Pass default values:

```php
{{ Form::checkbox('colors', array(
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

> The `Form::radio()` method uses the same signature as `Form::checkbox()`

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