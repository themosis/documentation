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
- [Retrieve form fields](#retrieve-form-fields)
    - [By name](#by-name)
    - [By group](#by-group)
    - [All fields](#all-fields)
- [Retrieve form data](#retrieve-form-data)
- [Change form prefix](#change-form-prefix)

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

Now from our `pages.contact` Blade view, we can display the form by calling its `render` method like so:

```html
@extends('layouts.main')

@section('content')
    <h1>Contact Us</h1>
    {!! $form->render() !!}
@endsection
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

Retrieve form fields
--------------------

Each form instance contains a `FieldRepositoryInterface` instance. The fields repository class provides method to get fields defined inside your form.

In order to access the fields repository class, call the form `repository` method:

```php
$form = $this->form(new ContactForm());
$repository = $form->repository();
```

### By name

The fields repository class has a `getFieldByName` method you can use to retrieve a specific field instance from your form by passing the field original (not the prefixed one) name value:

```php
$form = $this->form(new ContactForm());

$fullnameField = $form->repository()->getFieldByName('fullname');
$emailField = $form->repository()->getFieldByName('email');
$messageField = $form->repository()->getFieldByName('message');
```

### By group

By default, a form instance is organizing all attached fields to a `default` group. Think of a form group as a form section containing its own inner fields.

> See organizing form fields for more information about form groups.

The repository also provides a method where you can grab a collection of fields by their group name using the `getFieldsByGroup` method and passing the group name as the argument:

```php
$form = $this->form(new ContactForm());

$fields = $form->repository()->getFieldsByGroup('default');
```

On a form using only the `default` group, the function is returning all its fields.

### All fields

The repository also has an `all` method where you can fetch all your form fields from all its groups as well:

```php
$form = $this->form(new ContactForm());

$allFields = $form->repository()->all();
```

The `all` method return an array of fields instances.

Retrieve form data
------------------


