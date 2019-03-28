Form
====

- [Introduction](#introduction)
- [Create a form](#create-a-form)
    - [Add fields](#add-fields)
    - [Add validation](#add-validation)
- [Render a form](#render-a-form)
- [Error reporting](#error-reporting)
    - [Perform action on successful validation](#perform-action-on-successful-validation)
    - [Persist form data on successful validation](#persist-form-data-on-successful-validation)
    - [Customize error messages](#customize-error-messages)
- [Retrieve form fields](#retrieve-form-fields)
    - [By name](#by-name)
    - [By group](#by-group)
    - [All fields](#all-fields)
- [Retrieve form data](#retrieve-form-data)
    - [Using a data object](#using-a-data-object)
    - [Using the repository](#using-the-repository)
- [Form options](#form-options)
    - [Attributes](#attributes)
    - [Errors](#errors)
    - [Flush](#flush)
    - [WordPress nonce](#wordpress-nonce)
    - [Tags](#tags)
    - [Theme](#theme)
- [Form prefix](#form-prefix)
- [Organizing form fields](#organizing-form-fields)

Introduction
------------

The Themosis framework provides a new form class to help you build HTML forms. It includes methods to construct the form inner fields, methods for validation and error reporting by leveraging the `illuminate/validation` package.

Create a form
-------------

In order to create a new form, you need to create a `Formidable` class instance. From the terminal, run the `make:form` command. Below is an example of a basic "Contact Form":

```bash
php console make:form ContactForm
```

The scaffold command will create a new `ContactForm` class and store it inside the `app/Forms` directory:

```php
<?php

namespace App\Forms;

use Themosis\Field\Contracts\FieldFactoryInterface;
use Themosis\Forms\Contracts\FormFactoryInterface;
use Themosis\Forms\Contracts\Formidable;
use Themosis\Forms\Contracts\FormInterface;

class ContactForm implements Formidable
{
    /**
     * Build your form.
     *
     * @param FormFactoryInterface  $factory
     * @param FieldFactoryInterface $fields
     *
     * @return FormInterface
     */
    public function build(FormFactoryInterface $factory, FieldFactoryInterface $fields): FormInterface
    {
        // Create and return your form...
    }
}
```

In order to generate your form, you need to define it inside the `build` method and return a `FormInterface` instance. Use the `FormFactoryInterface` to create a form instance and call the `make` method:

```php
public function build(FormFactoryInterface $factory, FieldFactoryInterface $fields): FormInterface
{
    return $factory->make()
        ->get();
}
```

Use the `get` method on the form factory to return the instance. The above code will generate a `<form></form>` HTML tag with some default attributes once rendered on a view.

Let's add a few input to the form!

### Add fields

You can add as many fields as you want to your form. Each added field has shared as well as unique properties and render its own HTML thanks to a view file attached to it.

In order to add a field to your form, you may use the `add` method on your form and use the `FieldFactoryInterface` instance to pass a field instance to it. Based on our `ContactForm` example, let's add a text input for full name, an email input, a textarea input and a submit button:

```php
public function build(FormFactoryInterface $factory, FieldFactoryInterface $fields): FormInterface
{
    return $factory->make()
        ->add($fields->text('fullname'))
        ->add($fields->email('email'))
        ->add($fields->textarea('message'))
        ->add($fields->submit('send', [
            'label' => 'Contact Us'
        ]))
        ->get();
}
```

In the above example, the form will display 4 inputs:

1. A text input with a name attribute of `th_fullname`
2. An email input with a name attribute of `th_email`
3. A textarea input with a name attribute of `th_message`
4. A submit input with a name attribute of `th_send`

The form API automatically prefix your input name attribute values in order to avoid conflict with WordPress restricted query vars. You could use a `name` value for your field for example and it will render by default with a name attribute of `th_name`.

> The default prefix is `th_` but you can provide your own [form prefix](#change-form-prefix) as well.

Our contact form example does not yet handle input validation as no rules have been defined on the fields. Let's add some basic validation rules to our form fields!

### Add validation

Form validation is managed by the `illuminate/validation` package. In order to validate form fields, you may pass a `rules` option to each field you want to validate on the server side:

```php
public function build(FormFactoryInterface $factory, FieldFactoryInterface $fields): FormInterface
{
    return $factory->make()
        ->add($fields->text('fullname', [
            'rules' => 'required|min:3'
        ]))
        ->add($fields->email('email', [
            'rules' => 'required|email'
        ]))
        ->add($fields->textarea('message', [
            'rules' => 'required|min:30'
        ]))
        ->add($fields->submit('send', [
            'label' => 'Contact Us'
        ]))
        ->get();
}
```

You can find a [full list of validation rules](https://laravel.com/docs/validation#available-validation-rules) on the official documentation.

Once you add validation rules to your form fields, error reporting is not enabled by default. In order for validation to work, you must pass a `Request` object to your form. Please read the [error reporting](#error-reporting) section below for more details regarding validation.

Now, let's get our form and render it on a view!

Render a form
-------------

In its most basic way, in order to display a form in a view, call the `render` method from your form instance.

Below is an example where we instantiate a `ContactForm` from a controller and pass it to a custom view. Then, from the view, we render the form.

```php
<?php

namespace App\Http\Controllers;

use App\Forms\ContactForm;
use Illuminate\Http\Request;

class PageController extends Controller
{
    public function contact(Request $request)
    {
        $form = $this->form(new ContactForm());

        return view('pages.contact', [
            'form' => $form
        ]);
    }
}
```

When creating a new form from a controller class, you can leverage the `form` helper method in order to build it. If you want to generate your form outside a controller, you may do it like so:

```php
$form = (new ContactForm())->build(app('form'), app('field'));
```

### Blade
Now from our `pages.contact` Blade view, we can display the form by calling its `render` method like so:

```html
@extends('layouts.main')

@section('content')
    <h1>Contact Us</h1>
    {!! $form->render() !!}
@endsection
```

### Twig
If you're using the twig rendering system, you'll be able to render form with the following method + filter:

```twig
    {{ form.render()|raw }}
```

Error reporting
---------------

The form handles validation as well as error reporting. By default, if a field validation failed, the form has a `not valid` status and the field, if rendered back to a view, is rendering an error message below its input.

In order to trigger validation on a form, you need to pass it the `Request` instance. All controllers methods can capture a `Request` instance by type-hinting it as one of the method argument.

Following our `ContactForm` sample, let's enable validation on the form by calling the form `handleRequest` method like so from a controller method listening on a POST request:

```php
<?php

namespace App\Http\Controllers;

use App\Forms\ContactForm;
use Illuminate\Http\Request;

class PageController extends Controller
{
    public function sendContactConfirmation(Request $request)
    {
        $form = $this->form(new ContactForm());
        
        $form->handleRequest($request);

        return view('pages.contact', [
            'form' => $form
        ]);
    }
}
```

The above code will receive sent data through the `Request` instance. The request object is then pass to the form using the `handleRequest` method.

Validation is done directly on the `handleRequest` method. So in the above example, we don't do anything special with the submitted data. The form instance is sent back to the same contact view and render its content on a POST request.

If validation is successful, the form is flushing all its data by default. So the rendered page is an empty form. If the validation failed, the form will keep valid data and flush invalid one. For fields where validation failed, the form is also rendering an error message below each field.

### Perform action on successful validation

After calling the form `handleRequest` method, the form changes its internal state. If validation is successful after passing the `Request` instance, you can call the form `isValid` method to perform action like so:

```php
<?php

namespace App\Http\Controllers;

use App\Forms\ContactForm;
use Illuminate\Http\Request;

class PageController extends Controller
{
    public function sendContactConfirmation(Request $request)
    {
        $form = $this->form(new ContactForm());
        
        $form->handleRequest($request);
        
        if ($form->isValid()) {
            // Send an email confirmation...and redirect...
        }
    }
}
```

On the contrary, the form has also a `isNotValid` method if you need to perform some action on failed validation:

```php
<?php

namespace App\Http\Controllers;

use App\Forms\ContactForm;
use Illuminate\Http\Request;

class PageController extends Controller
{
    public function sendContactConfirmation(Request $request)
    {
        $form = $this->form(new ContactForm());
        
        $form->handleRequest($request);
        
        if ($form->isNotValid()) {
            // Redirect back...
        }
    }
}
```

### Persist form data on successful validation

> For now, the form instance is not yet managing form data through a value object instance. This solution is a temporary approach to retrieve form valid data after a successful validation.

As mentioned above, each time a validation is successful on a form, the object flush its field data. In order to avoid losing validated data after validation, you can pass a `flush` option to your form `make` method like so:

```php
<?php

namespace App\Forms;

use Themosis\Field\Contracts\FieldFactoryInterface;
use Themosis\Forms\Contracts\FormFactoryInterface;
use Themosis\Forms\Contracts\Formidable;
use Themosis\Forms\Contracts\FormInterface;

class ContactForm implements Formidable
{
    /**
     * Build your form.
     *
     * @param FormFactoryInterface  $factory
     * @param FieldFactoryInterface $fields
     *
     * @return FormInterface
     */
    public function build(FormFactoryInterface $factory, FieldFactoryInterface $fields): FormInterface
    {
        return $factory->make([
            'flush' => false
        ])
            ->add($fields->text('fullname', [
                'rules' => 'required|min:3'
            ]))
            ...
    }
}
```

By setting the `flush` option to `false`, the form will keep its data after a successful validation so you can now retrieve its validated data.

### Customize error messages

All validation rules have an error message attached to them by default. The list of errors messages is stored at application root inside the `resources/languages/en_US/validation.php` file.

> Note that the framework is also providing error messages in french for France within the `fr_FR` directory and for Belgium in the `fr_BE` folder.

The `validation.php` file is a global list of messages for your application. You could customize your form error messages from there but please note that any change applied inside that validation file is applicable on all your application forms: front-end forms, metabox fields, page settings, ...

If you want to customize the error message for your form, it is best to pass a `messages` option to your field. The `messages` option accept an array of key/value pairs where the key is the name of the validation rule and its value, the validation message.

For example, let's customize the errors messages for the contact form:

```php
public function build(FormFactoryInterface $factory, FieldFactoryInterface $fields): FormInterface
{
    return $factory->make()
        ->add($fields->text('fullname', [
            'rules' => 'required|min:3',
            'messages' => [
                'min' => 'Your :attribute should contain 3 characters at least.'
            ]
        ]))
        ->add($fields->email('email', [
            'rules' => 'required|email',
            'messages' => [
                'required' => 'Oh dear, please provide your :attribute.'
            ]
        ]))
        ->add($fields->textarea('message', [
            'rules' => 'required|min:20'
        ]))
        ->add($fields->submit('submit'))
        ->get();
}
```

> Note the use of the `:attribute` placeholder. This attribute placeholder is replaced by the field name (without its prefix) inside the message. So, based on the example above, the full name minimum message is: "Your fullname should contain 3 characters at least."

You can also customize the value of the `:attribute` placeholder. You can change the attribute value by passing a `placeholder` option to your field like so:

```php
// Message: Your full name should contain 3 characters at least.
$fields->text('fullname', [
    'messages' => [
        'min' => 'Your :attribute should contain 3 characters at least.'
    ],
    'placeholder' => 'full name',
])

// Message: Oh dear, please provide your e-mail address.
$fields->email('email', [
    'messages' => [
        'required' => 'Oh dear, please provide your :attribute.'
    ],
    'placeholder' => 'e-mail address'
])
```

Retrieve form fields
--------------------

Each form instance contains a `FieldRepositoryInterface` instance. The fields repository class provides methods to get fields defined inside your form.

In order to access the fields repository class, call the form `repository` method:

```php
$form = $this->form(new ContactForm());
$repository = $form->repository();
```

### By name

The fields repository class has a `getFieldByName` method you can use to retrieve a specific field instance from your form by passing the field original name (not the prefixed one) value:

```php
$form = $this->form(new ContactForm());

$fullnameField = $form->repository()->getFieldByName('fullname');
$emailField = $form->repository()->getFieldByName('email');
$messageField = $form->repository()->getFieldByName('message');
```

### By group

By default, a form instance is organizing all attached fields to a `default` group. Think of a form group as a form section containing its own inner fields. At render time, each group is also contained in its own div element.

> See [organizing form fields](#organizing-form-fields) for more information about form groups.

The repository also provides a method where you can grab a collection of fields by their group name using the `getFieldsByGroup` method and passing the group name as the argument:

```php
$form = $this->form(new ContactForm());

$fields = $form->repository()->getFieldsByGroup('default');
```

On a form using only the `default` group, the function is returning all fields.

### All fields

The repository also has an `all` method where you can fetch all your form fields from all its groups at once:

```php
$form = $this->form(new ContactForm());

$allFields = $form->repository()->all();
```

The `all` method return an array of fields instances.

Retrieve form data
------------------

After validation, you may access the form valid data in order to perform specific actions. The recommended approach is to use a "data object" representing the form data or by using the form fields repository instance in order to fetch fields objects and retrieve their value individually.

### Using a data object

> This feature is not yet implemented but is coming for the 2.0 stable release.

### Using the repository

Before being able to fetch data from the form repository, make sure to set the form `flush` option to `false` in order to persist validated data after a successful validation.

As mentioned above, using the fields repository, you can access each field instance and return their data by using the `getValue` method like so:

```php
<?php

namespace App\Http\Controllers;

use App\Forms\ContactForm;
use Illuminate\Http\Request;

class PageController extends Controller
{
    public function sendContactConfirmation(Request $request)
    {
        $form = $this->form(new ContactForm());
        $form->handleRequest($request);
        
        if ($form->isValid()) {
            $fullname = $form->repository()->getFieldByName('fullname')->getValue();
            $email = $form->repository()->getFieldByName('email')->getValue();
            ...
            // Send confirmation and redirect...
        }
    }
}
```

When using the repository methods, use the original name passed when building your form fields. The object make sure to ignore the prefix value as this is only used at render time.

Form options
------------

Each form instance has a list of options that controls its behavior. In order to customize your form, you can pass an array of options to the form factory `make` method:

```php
return $factory->make([
    'attributes' => [
        'id' => 'contact-form'
    ],
    'flush' => false,
    ...
]);
```

Here is a list of the form options available on each instance: `attributes`, `errors`, `flush`, `nonce`, `nonce_action`, `referer`, `tags` and `theme`.

### Attributes

You can control the form tag HTML attributes by passing an `attributes` option to your form and passing an array of key/value pairs. The key is representing the HTML attribute name and the value the attribute value.

You can, for example, specify the `action` attribute value of your form in order to send the request to another URL like so:

```php
public function build(FormFactoryInterface $factory, FieldFactoryInterface $fields): FormInterface
{
    return $factory->make([
        'attributes' => [
            'action' => route('settings'), // return http://domain.com/settings URL
            'id' => 'contact-form',
            'class' => 'form-control'
        ]
    ])
        ...
        ->get();
}
```

### Errors

The `errors` option allows you to inform the form to display or not the errors messages after validation. By default, after a failed validation, if you render the form, each field is displaying its error message below its input. If you want to control where to display form errors on your page, then you can set the `errors` option to `false` and then leverage the `errors` method of your form instance to get the list of messages to display.

```php
return $factory->make([
    'errors' => false
])
...
->get();
```

By setting the `errors` option to `false`, your form is no longer displaying errors messages. Use the form `errors` method to fetch the messages like so:

```php
public function sendContactConfirmation(Request $request)
    {
        $form = $this->form(new ContactForm());
        $form->handleRequest($request);
        
        if ($form->isNotValid()) {
            return view('pages.contact', [
                'form' => $form,
                'errors' => $form->errors()
            ]);
        }
    }
```

The `errors` method is returning a `Illuminate\Contracts\Support\MessageBag` instance. From your view, you can loop through all errors messages like so:

```php
<h1>Contact</h1>
@if($errors->any())
    <ul>
        @foreach($errors->all() as $error)
            <li>{{ $error }}</li>
        @endforeach
    </ul>
@endif
{!! $form->render() !!}
```

### Flush

The `flush` option, as already mentioned, allows you to remove or not validated data after a successful validation. By default the value is set to `true` meaning that the data is removed upon validation. If you want to persist the data in your form instance, set the `flush` option value to `false`.

```php
return $factory->make([
    'flush' => false
])
...
->get();
```

### WordPress nonce

By default, all form instances generate a WordPress nonce field. By default, the nonce name is set to `_themosisnonce` with a default action value of `form`.

It is best to customize the nonce field per form. You can do so by providing a `nonce` and/or a `nonce_action` options to your form instance. Generally, you may want to keep the default nonce name `_themosisnonce` but it is recommended to change the action verb like so:

```php
return $factory->make([
    'nonce_action' => 'contact_us'
])
...
->get();
```

Also, the WordPress nonce uses a `referer` boolean value. If set to `true`, a referrer field is also adding into the form (default). If set to `false`, no referrer value is defined for the request:

```php
return $factory->make([
    'nonce_action' => 'contact_us',
    'referer' => false
])
...
->get();
```

### Tags

The `tags` option controls the output of the form HTML tags. If for example, your form should be embedded inside an existing form (for example, inside a WordPress administration page), you may need to output only its fields by setting the `tags` option to `false` like so:

```php
return $factory->make([
    'tags' => false
])
...
->get();
```

### Theme

A form and its fields are rendered through a group of pre-defined views. Each group of views is a theme. By default, each form is rendered using the `themosis` theme which output the HTML tags with custom CSS classes in order to customize the look of your form.

The framework also handles a `bootstrap` theme, which output form elements using the BootstrapCSS classes. In order to change the form theme, simply provide a `theme` option with the group name as a value like so:

```php
return $factory->make([
    'theme' => 'bootstrap'
])
...
->get();
```

When a theme is applied from the form, the theme is applied to its inner fields directly.

Form prefix
-----------

By default, a form is prefixing all passed name attribute values. This mechanism is provided by default in order to avoid conflict with WordPress reserved query vars.

The default `th_` prefix is applied to all form fields. In order to change the prefix for your form fields, you may call the `setPrefix` method on the form instance like so:

```php
public function build(FormFactoryInterface $factory, FieldFactoryInterface $fields): FormInterface
{
    $form = $factory->make()
        ->add($fields->text('fullname', [
            'rules' => 'required|min:3'
        ]))
        ...
        ->get();
        
    return $form->setPrefix('custom_');
}
```

Organizing form fields
----------------------

The new form API allows you to group fields. This grouping capability does not affect the form behavior in its data management but does change how the form is rendered.

At render time, the form is wrapping each group with their associated fields on a div element. The group feature allows better customization in order to style your form inside your view.

In order to group your form fields, you need to pass a `group` option to each one of your fields:

```php
public function build(FormFactoryInterface $factory, FieldFactoryInterface $fields): FormInterface
{
    return $factory->make()
        ->add($fields->text('firstname', [
            'group' => 'main'
        ]))
        ->add($fields->text('lastname', [
            'group' => 'main'
        ]))
        ->add($fields->email('email', [
            'group' => 'main'
        ]))
        ->add($fields->textarea('message'))
        ->add($fields->submit('send', [
            'label' => 'Contact Us'
        ]))
        ->get();
}
```

In the above example, the last name, first name and email fields are grouped under the `main` group while other fields are grouped to the `default` one. The group name is arbitrary. The API is grouping fields based on a similar group name.

You can then render the form inside your view using the `render` method:

```php
{!! $form->render() !!}
```

Based on the above example, the first 3 fields are wrapped in a separate div element.

Next
----

You can read the following documentation:
* [Field guide]({{url}}/field)
