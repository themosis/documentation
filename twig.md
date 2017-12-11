Twig
====

- [Introduction](#introduction)
- [Basic usage](#basic-usage)
- [Twig control structures](#twig-control-structures)
	- [Include views](#include-views)
	- [Pass data to included views](#pass-data-to-included-views)
	- [Sections](#sections)
	- [Echo data](#echo-data)
	- [Conditional statements](#conditional-statements)
	- [Loop statements](#loop-statements)
	- [Displaying raw text](#displaying-raw-text)
	- [Comments](#comments)
- [Extending Twig](#extending-twig)
- [Themosis Twig Extension](#themosis-twig-extension)
- [Poedit](#poedit)

Introduction
------------

The Themosis framework now adds support to the Twig template engine. For a more detailed presentation of the Twig engine, please check the official documentation:

- [Twig official documentation](http://twig.sensiolabs.org/)

In this documentation, you'll find the basics operations on how to get started with the Twig engine as well as the Twig extension the Themosis framework has added in order to help you work with WordPress and its core functions within your views. For more advanced topics regarding Twig, please refer to the official documentation.

Basic usage
-----------

In order to use the Twig engine, all your view files should use the `.twig` extension.

Here is an example of a basic view, using Twig, stored inside the `resources/views` folder:

```twig
<!-- View stored in resources/views/welcome.twig -->
{% extends 'layouts/main.twig' %}

{% block main %}
    <h1>Hello, {{ name }}</h1>
{% endblock %}

{% block sidebar %}
    <h3>Latest posts</h3>
    <ul>
        {% for item in items %}
            <li>{{ item.post_title }}</li>
        {% endfor %}
    </ul>
{% endblock %}
```

And this view may be returned to the browser like so:

```php
Route::get('home', function ($post, $query) {
    $posts = $query->get_posts();
    return view('welcome', ['name' => 'Julien', 'items' => $posts]);
});
```

In the previous example, we used the `{% extends 'layouts/main' %}` syntax. This function allows you to use layouts:

```twig
<!-- Layout stored in resources/views/layouts/main.twig -->
{{ include('header.twig') }}
    <div class="container">
        {% block main %}{% endblock %}
    </div>

    <div class="sidebar">
        {% block sidebar %}	
            <p>Sidebar section from the "main.twig" layout file.</p>
        {% endblock %}
    </div>
{{ include('footer.twig') }}
```

> Compared to the Blade engine, in Twig, you cannot omit the `.twig` file extension when refering to a view layout or a view you would like to include. Another comparison, is the path to your views. Where Blade uses a dot notation, with Twig, you stick to classic path notation using the `/` symbol.

Twig control structures
------------------------

### Include views

```twig
{{ include('header.twig') }}
```

This command can include a view file called `header.twig` only.

> Note regarding views inheritance. Included views can access variables defined from their parent view. So compared to Blade, you don't to pass a second argument array with view variables. See also the official documentation for [template inheritance.](http://twig.sensiolabs.org/doc/2.x/templates.html#template-inheritance)

### Sections

#### Overwrite section

Add the `{% block %}` and `{% endblock %}` statements to overwrite a layout section.

```twig
{% extends 'layouts/main.twig' %}

{% block sidebar %}
    <p>New sidebar content</p>
{% endblock %}
```

#### Extend parent section

Add the `{{ parent() }}` function in order to keep the content of the parent section defined in the layout view.

```twig
{% extends 'layouts/main.twig' %}

{% block sidebar %}
    {{ parent() }}
    <p>Child sidebar content appended to parent's content</p>
{% endblock %}
```

### Echo data

```twig
Hello {{ name }}
```
By default the double curly braces echo the data but DO NOT escape it.

> In Twig, no need to prepend your variable name with a `$` sign. Simply refer the name only between the curly braces.

#### Echo escaped data

In Twig, in order to print escaped data, you'll use `filters`. The `e` filter tells Twig to escape the variable value before it gets printed. Here is an example:

```twig
Hello {{ name|e }}
```
In order to use a filter, simply add a `|` sign followed by one or multiple filters. The `e` filter escapes HTML by default. Check the [escape filter](http://twig.sensiolabs.org/doc/2.x/filters/escape.html) documentation for more details. You can also get a list of all [Twig filters here.](http://twig.sensiolabs.org/doc/2.x/filters/index.html)

#### Echoing data after checking for existence

By default you could write the following statement:

```twig
{{ isset(name) ? name : 'Default' }}
```
Instead of writing a ternary statement, Twig allows you to use the following convenient short-cut:

```html
{{ name ?? 'Default' }}
```

### Conditional statements
#### If

```twig
{% if value %}
    <p>The value is {{ value }}.</p>
{% elseif value is iterable %}
    <p>The value is an array.</p>
{% else %}
    <p>Something is wrong, there is no value.</p>
{% endif %}
```

In Twig, you cannot run PHP functions like you do in a classic PHP file or Blade view. Twig provides multiple helpers to meet your needs through the use of filters, tags, tests. In the code example from above, we use the test function `iterable` in order to check that our value is an array like value (which is identical to `is_array($value)`).

> The Themosis framework Twig extension provides helpers to call core PHP and WordPress functions. [Read the details below.](#themosis-twig-extension)

### Loop statements
#### For - Foreach

In Twig, there is one `for` tag to loop through an iterable variable.

Using a number sequence like in a PHP for loop `for ($i = 0; $i < 10; $i++)`:

```twig
<ul>
    {% for i in 0..9 %}
        <li>Item {{ i }}</li>
    {% endfor %}
</ul>
```

Using an associative array and iterate over keys and values like a PHP foreach:

```twig
{% for key, value in items) %}
    <p>This {{ value }} opens that {{ key }}.</p>
{% endfor %}
```

> Check the [official documentation](http://twig.sensiolabs.org/doc/2.x/tags/for.html) for more information about the `for` tag.

### Displaying raw text

If you need to display a string that is wrapped in curly braces, you may escape the Twig behavior by surrounding your code with the `verbatim` tag like so:

```twig
{% verbatim %}
    {{ This is not processed by Twig }}
{% endverbatim %}
```

### Comments

Use the `{# ... #}` statements to add comments into your Twig view:

```twig
{# This comment will not be rendered in HTML #}
```

Extending Twig
--------------

The Twig template engine can be extended and provides extra features needed for your project.
In this section, we're not going through what you can do to extend Twig but rather how to retrieve the engine instance.

If you want to explore what kind of extensions you can add to Twig, please read the [official documentation.](http://twig.sensiolabs.org/doc/2.x/advanced.html)

Now, in order to register your extensions, you need to fetch the Twig Environment instance and call its `addExtension` method:

```php
container('twig')->addExtension(new \Twig_Extension_Optimizer())
```

> We recommend you to register Twig extensions from a service provider. Check the [Service provider guide]({{url}}/service) for more information.

Themosis Twig Extension
-----------------------

The Themosis framework provides a Twig extension with multiple helpers in order to ease your transition to the engine for your templates/views.

### Functions

Twig do not let you use PHP functions inside your `.twig` templates like you would in a classic PHP file. Meaning that from a Twig template, you cannot make this for example:

```php
<p>{{ ucfirst(name) }}</p>
```

So in order to bring the use of PHP functions, the Themosis framework Twig extension has added 2 helpers:

- A function `fn`
- A `fn` namespace

The `fn` keyword is for `function` and let you call any PHP function.

#### Function fn

The Twig function `fn` first argument is the name of the PHP function you want to call, and following arguments are arguments to pass to the original PHP function. Here is an example:

```twig
<h1>{{ fn('bloginfo', 'name') }}</h1>
```

which calls the WordPress function `bloginfo('name')`.

Here is another example from our `ucfirst` sample from above:

```twig
<p>{{ fn('ucfirst', name) }}</p>
```

#### Namespace fn

The namespace `fn` behaves exactly like the global `fn` and lets you call any PHP functions with a different syntax like so:

```twig
<h1>{{ fn.bloginfo('name') }}</h1>
```

The namespace `fn` is easier to use as it lets you call PHP functions like "usual".Here is the example with the `ucfirst` function:

```twig
<p>{{ fn.ucfirst(name) }}</p>
```

### Form global

We've added a `Form` global allowing developers to use the Themosis Form API directly into their Twig templates. Indeed developers are used to use it into Blade template like so in order to create HTML forms:

```php
// From a Blade view
{{ Form::open() }}
    {{ Form::text('username') }}
{{ Form::close() }}
```

Now from a Twig template:

```twig
{{ Form.open() }}
    {{ Form.text('username') }}
{{ Form.close() }}
```

Please note the dot syntax in order to call Form class methods.

> Check the [Form guide]({{url}}/form) for a list of available methods.

### WordPress functions

By default you can call any PHP and WordPress functions using our `fn` helper. But in order to ease the development we've already added some most used WordPress functions to Twig. This means that you can call the following functions like you're used to within your Twig template.

Here is the list of already defined WordPress functions for Twig:

- wp_head()
- wp_footer()
- body_class()
- post_class()
- wpautop()
- wp_trim_words()
- meta()

and we've also added WordPress gettext functions:

- translate()
- __()
- _e()
- _n()
- _x()
- _ex()
- _nx()
- _n_noop()
- _nx_noop()
- translate_nooped_plural()

Here is an example on how to call these functions within your Twig view:

```twig
<html>
<head>
    {{ wp_head() }}
</head>
<body {{ body_class('custom-class') }}>
    {% for post in posts %}
        <article>
            <h2>{{ post.post_title|title }}</h2>
            <div>{{ wpautop(post.post_content) }}</div>
            <a href="{{ fn.get_permalink(post.ID) }}">{{ __('Read more', 'text-domain') }}</a>
        </article>
    {% endfor %}
    {{ wp_footer() }}
</body>
</html>
```

Poedit
------

By default, Poedit cannot find strings to translate from Twig templates. Here is a list of parameters to add to your Poedit software preferences so it can detect translation strings from your Twig views:

1. Open Poedit
2. Go to Preferences -> Parsers
3. Add a new parser with following settings:
	- Language: `Twig`
	- Extension: `*.twig`
	- Parser command: `xgettext --language=Python --add-comments=TRANSLATORS --force-po -o %o %C %K %F`
	- An item in keyword list: `-k%k`
	- An item in input files list: `%f`
	- Source code charset: `--from-code=%c`
4. Click save

In order for this to work, you must have a poedit project correctly setup pointing to the `languages` folder of either your theme or custom plugin and define the gettext methods and resources base path. From the catalog preferences, set the sources paths like so:

- Base path: `../`
- Paths:
    - `resources`
    - `resources/admin`
    - `resources/views`

as well as adding the following gettext methods to the source keywords catalog tab:

- `__`
- `_e`
- `_x`
- `esc_html__`
- `esc_html_e`
- `_n`
- `_nx`
- `_n_noop`
- `_nx_noop`
- `_ex`

and more if needed.
