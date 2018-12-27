Form
====

- [Introduction](#introduction)
- [Create a form](#create-a-form)
    - [Add fields](#add-fields)
    - [Add validation](#add-validation)
- [Render a form](#render-a-form)
- [Error reporting](#error-reporting)
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

The form API automatically prefix your input name attributes value in order to avoid conflict with WordPress restricted query vars. You could use a `name` value for your field for example and it will render by default with a value of `th_name`.

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

You can find a [full list of validation rules](https://laravel.com/docs/5.7/validation#available-validation-rules) on the official documentation.

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

