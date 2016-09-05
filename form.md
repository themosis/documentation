Form
====

- [Opening a form](#opening-a-form)
- [Labels](#labels)
- [Text, password, email, number, date, hidden inputs](#text-password-email-number-date-hidden-input)
- [Textarea](#textarea)
- [Checkbox and radio inputs](#checkbox-and-radio-inputs)
- [Select field](#select-field)
- [Buttons](#buttons)
- [Miscellaneous inputs](#miscellaneous-inputs)

Opening a form
--------------

```php
{{ Form::open() }}
	// Form inputs
{{ Form::close() }}
```

Form::open($action = null, $method = 'POST', $ssl = false, $attributes = [])

- **$action**: _string_ The action attribute for the opening form tag.
- **$method**: _string_ The method attribute. Default to 'post'.
- **$ssl**: _boolean_ Tell the form to change the method protocol to `https` if using ssl. Default to false.
- **$attributes**: _array_ Extra attributes and properties for the form. 

By default, the form sets the method attribute to `POST` and its action attribute to the current URL.

The `open()` method returns the opening form HTML tag and the `close()` method returns the closing form tag.

> When using the `Form::open()` method, it automatically adds WordPress nonce and http referer hidden fields into your form. The nonce field uses the `Session::nonceName` and `Session::nonceAction` constants to populate its value. Check below on how to check for nonce values when form is submitted.

### Define custom nonce values.

You can overwrite the default custom nonce fields used inside the `Form::open()` method.

In order to add your custom nonce, simply add them to the `$attributes` argument of the `Form::open()` method like so:

```php
{{ Form::open('', 'post', false, [
	'nonce'			=> 'action',
	'nonce_action'	=> 'edit-something'
]) }}
	// Inputs
{{ Form::close() }}
```
> Make sure to use the `nonce` key for the nonce field _name_ and the `nonce_action` key for its _value_.

### Set a custom action attribute

In order to set a custom action attribute, simply fill in the first parameter of the `open` form method:

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
> Set the first parameter as `empty string` or `null` will set the action attribute to the current URL.

### Make sure to submit your data to a SSL page

By setting the third parameter of the `open()` method to `true`, the action attribute will change its protocol to `https`.

```php
{{ Form::open('contact', 'post', true) }}
	// Form inputs
{{ Form::close() }}
```

### Add custom attributes to the opening form tag

You can pass as the fourth parameter an array in order to add custom attributes.

```php
{{ Form::open(null, 'post', false, [
	'data-message' 	=> 'The form message.',
	'enctype'		=> 'multipart/form-data',
	'id'			=> 'register-form'
]) }}
	// Form inputs
{{ Form::close() }}
```

### Check nonce values when a form is submitted

When you use the `Form::open()` method, it automatically outputs an opening form tag populated with WordPress nonce values. It is considered "best-practice" to check for those nonce values when your form is submitted before proceeding to some other actions.

If you check your page source code, you'll find two hidden fields:

- The first one is a NONCE value
- The second one is the HTTP REFERER value

As explained in the `open()` method, it uses two class's constants: `Session::nonceName` and `Session::nonceAction`.

Let's verify the nonce value regarding those two constants:

```php
// A form is submitted using the POST method.
if (wp_verify_nonce(Input::get(Session::nonceName), Session::nonceAction))
{
	// Proceed with data
}
```
The `nonceName` value is the nonce field name attribute. The `nonceAction` value is the nonce action string. Check `wp_verify_nonce()` function in the [codex](http://codex.wordpress.org/Function_Reference/wp_verify_nonce).

The example above is working in a front-end example. If you have a custom form on a custom page in the admin, check also the `check_admin_referer` function in the [codex](http://codex.wordpress.org/Function_Reference/check_admin_referer)

> Note: In the previous example, we use the Input class to grab POST data. Check the [validation guide](http://framework.themosis.com/docs/validation/) for the Input class use.

Labels
------

Output a label tag:

```php
// This will output <label>Display text</label>
{{ Form::label('Display text') }}
```

Add attributes to a label:

```php
// This will output <label for="my-input" class="super">Display text</label>
{{ Form::label('Display text', [
	'class'	=> 'super',
    'for'	=> 'my-input'
]) }}
```

Text, password, email, number, date, hidden inputs
--------------------------------------------------

Output a text input:

```php
// This will output <input type="text" name="name">
{{ Form::text('name') }}
```

Set the input value:

```php
// <input type="text" name="name" value="Text value"> 
{{ Form::text('name', 'Text value') }}
```

Add attributes to the text input:

```php
{{ Form::text('name', 'Default value', [
	'class'			=> 'super',
	'placeholder'	=> 'Insert your name here'
]) }}
```

> The `Form::password()`, `Form::email()`, `Form::number()`, `Form::date()` and `Form::hidden()` methods use the same signature.

Textarea
--------

The `Form::textarea()` method will output a `<textarea></textarea>` tag.

```php
{{ Form::textarea('name') }}
```

Set the textarea "value" by passing data to the second parameter:

```php
{{ Form::textarea('name', 'Text content') }}
```

Add attributes to the textarea tag by passing an array as the third parameter to the method:

```php
{{ Form::textarea('name', 'Text content', ['class' => 'regular-text']) }}
```

Checkbox and radio inputs
-------------------------

The `Form::checkbox()` helps you define one or multiple checkbox inputs.

Output one checkbox:

```php
// This will output <input type="checkbox" name="toggle[]" value="toggle">
{{ Form::checkbox('toggle', 'toggle') }}
```

> When checking for submitted data, the checkbox method register an array of values even for one checkbox.

Add custom attributes:

```php
{{ Form::checkbox('toggle', 'enabled', [], [
	'class'		=> 'super'
]) }}
```

### Multiple checkboxes and radio

In order to specify multiple checkboxes, simply pass an array of choices like so:

```php
{{ Form::checkbox('colors', [
	'red',
	'green',
	'blue'
]) }}
```

The first parameter is the common name of your checkboxes. The second parameter is an array of choices/options for your checkbox input.

By default, when you define multiple options/choices for your checkbox, the API is using the value as display text beside the checkbox. In order to define a custom display text for your checkbox options, define an array of key/value pairs like so:

```php
{{ Form::checkbox('colors', [
	'red'	=> 'Select red color',
	'green'	=> 'Choose the green',
	'blue'	=> 'Use the blue color'
]) }}
```

In order to set values or default values, pass an array as a third parameter to your checkbox method:

```php
{{ Form::checkbox('colors', [
	'red',
	'green',
	'blue'
], [
	'green'
]) }}
```

The above example will check the green checkbox by default.

Add attributes to your checkbox by passing an array as the fourth parameter:

```php
{{ Form::checkbox('colors', [
	'red', 
	'green',
	'blue'
], [], [
	'class'		=> 'super'
]) }}
```

This code adds attributes to the checkbox inputs. When you create a checkbox, the displayed text beside each checkbox is rendered within a label tag. You can define the attributes for these label tags by passing a `label` property to the checkbox attributes like so:

```php
{{ Form::checkbox('colors', ['red', 'green', 'blue'], [], [
	'label'	=> [
		'class' => 'label-class'
	]
]) }}
```

The example above will add the `label-class` class to each labels inside your checkbox input.

> The `Form::radio()` method uses the same signature as `Form::checkbox()`

Output a radio input:

```php
{{ Form::radio('gender', [
	'woman',
	'man'
]) }}
```

Select field
------------

Generate a select tag with indexed options values:

```php
{{ Form::select('country', [
	[
		'belgium',
		'france',
		'usa'
	]
]) }}
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
{{ Form::select('country', [
	[
		'be'	=> 'belgium',
		'fr'	=> 'france',
		'us'	=> 'usa'
	]
]) }}
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
{{ Form::select('country', [
	'Europe'	=> [
		'belgium',
		'france'
	],
	'America'	=> [
		'usa'
	]
]) }}
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
{{ Form::select('country', [
	'Europe'	=> [
		'be'	=> 'belgium',
		'fr'	=> 'france'
	],
	'America'	=> [
		'us'	=> 'usa'
	]
]) }}
```

### Pass default value(s)

You can pass a default value by filling in the third parameter of the `select()` method:

```php
{{ Form::select('country', [
	[
		'belgium',
		'france',
		'usa'
	]
], 0) }}
```
> This set the default value to indexed value `0` (belgium). For custom options, simply pass the custom value.
> When using multiple selection, pass in an array as default values.

### Add custom attributes

Like other form fields, pass an array as the fourth parameter for custom attributes.

```php
{{ Form::select('countries', [
	[
		'belgium',
		'france',
		'usa'
	]
], null, [
	'class'		=> 'super',
	'multiple'	=> 'multiple'
]) }}
```

Buttons
-------

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
{{ Form::button('toggle', 'Click me!', ['class' => 'super']) }}
{{ Form::submit('register', 'Sign up', ['id' => 'awesome']) }}
```

Miscellaneous inputs
--------------------

The `Form` class provides an `input()` method in order to create any type of input tags.

```php
Form::input($type, $name, $value = null, array $attributes = [])
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

Other resources:
----------------

Check the validation and input guide for documentation on how to retrieve and sanitize submitted data.

* [Validation and input]({{url}}/validation)