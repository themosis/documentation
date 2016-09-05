Validation
==========

The framework comes with a class to help you sanitize your inputs.

- [Get input data](#get-input-data)
	- [Get all submitted data](#get-all-submitted-data)
	- [Get a specific data](#get-a-specific-data)
	- [Return a default value if no data found](#return-a-default-value-if-no-data-found)
- [Validate](#validate)
	- [Validate single data](#validate-single-data)
	- [Validate multiple data](#validate-multiple-data)
	- [Rules parameters](#rules-parameters)
- [Validation rules](#validation-rules)

Get input data
--------------

The framework comes with an `Input` class that helps you retrieve form submitted data.

### Get all submitted data

```php
$data = Input::all();
```

This will return an array with all submitted data found in a request.

### Get a specific data

```php
$data = Input::get('name');
```
The code above returns the saved value for the `name` key.

### Return a default value if no data found

```php
$data = Input::get('name', 'Foo');
```

If no `name` value is found, the code returns the `Foo` value.

Validate
--------

### Validate single data

```php
$data = Validator::single($data, ['num', 'min:3']);
```
Pass an array of validation rules as a second parameter.

### Validate multiple data

```php
$data = Validator::multiple(Input::all(), [
    'field-name'	 => ['alnum', 'min:5'],
    'email'      => ['email'],
    'age'        => ['num']
]);
```

Pass an array of validation rules as a second parameter where the `key` is the data field name and its value is an array of validation rules.

### Rules parameters

Some rules accept extra parameters in order to validate your data. Simply add a colon after your rule followed by parameters separated by a comma.

```php
$data = Validator::single($url, ['url:http, https']);

$data = Validator::single($age, ['num', 'min:1', 'max:2']);
```

Check the available rules below.

Validation rules
----------------

- **alpha**: Validate alphabetic characters.
- **num**: Validate only numeric characters.
- **negnum**: Validate negative numeric characters.
- **alnum**: Validate alphanumeric characters.
- **textfield**: Validate a textfield value. Uses the WordPress `sanitize_text_field` function.
- **textarea**: Validate a value from a textarea field. Uses the WordPress `esc_textarea` function.
- **html**: Validate a HTML value. Uses the WordPress `esc_html` function.
- **email**: Validate an email value. Uses the WordPress `sanitize_email` function.
- **url**: Validate an URL value. Uses the WordPress `esc_url` function. You can pass arguments to the rule like so: `['url:http, https']`. See the [codex](http://codex.wordpress.org/Function_Reference/esc_url) for the list of protocols you can pass to the rule.
- **min**: Validate a value if it has a minimum length. You have to pass arguments to the rule like so: `['min:5']`.
- **max**: Validate a value if it has a maximum length. You have to define arguments to the rule like so: `['max:25']`.
- **bool**: Validate a boolean value. Returns `true` if data equal to `true`, `1`, `on` and `yes`. Returns false otherwise.
- **kses**: Validate a value for allowed HTML tags. Uses the WordPress `wp_kses` function. You can pass arguments like so: `['kses:a|href|title, p']`.
- **hex**: Validate a hexadecimal value.
- **color**: Validate a color hexadecimal value like `#2abb6f` or `#ddd`.
- **file**: Validate a file extension value. You have to pass arguments to the rule like so: `['file:jpg, gif, png']`.
