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

```



