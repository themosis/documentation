Blade
=====

- [Introduction](#introduction)
- [Basic usage](#basic-usage)
- [Blade directives](#blade-directives)
    - [Include views](#include-views)
    - [Pass data to included views](#pass-data-to-included-views)
    - [Sections](#sections)
    - [Components and slots](#components-and-slots)
    - [Echo data](#echo-data)
    - [Conditional statements](#conditional-statements)
    - [Loop statements](#loop-statements)
    - [Displaying raw text](#displaying-raw-text)
    - [Comments](#comments)
    - [Javascript frameworks](#javascript-frameworks)
    - [Stacks](#stacks)
    - [Service injection](#service-injection)
- [Extending Blade](#extending-blade)
- [Themosis directives](#themosis-directives)
    - [Loop directive](#loop-directive)
    - [Query directive](#query-directive)
    - [Head directive](#head-directive)
    - [Footer directive](#footer-directive)
    - [Template directive](#template-directive)
- [Loop helper](#loop-helper)
- [Poedit](#poedit)

Introduction
------------

The Blade template engine is bundled with the `illuminate/view` package. When working with the Themosis framework, you automatically get access to it in order to work out your views.

- Read [Blade](https://laravel.com/docs/5.7/blade) official documentation for more details about the template engine features.

Basic usage
-----------

In order to use the Blade engine, all your view files should use the `.blade.php` extension.

Here is an example of a basic view, using Blade, stored inside the theme `views` folder:

```html
<!-- View stored in views/welcome.blade.php -->
@extends('layouts.main')

@section('main')
    <h1>Hello, {{ $name }}</h1>
@endsection

@section('sidebar')
    <h3>Latest posts</h3>
    <ul>
        @foreach($items as $item)
            <li>{{ $item->post_title }}</li>
        @endforeach
    </ul>
@endsection
```

And this view may be returned to the browser like so:

```php
Route::any('/', function ($post, $query) {
    $posts = $query->get_posts();
    return view('welcome', ['name' => 'Julien', 'items' => $posts]);
});
```

In the previous example, we used the `@extends` syntax. This function allows you to use layouts:

```html
<!-- Layout stored in views/layouts/main.blade.php -->
@include('header')
    <div class="container">
        @yield('main')
    </div>

    <div class="sidebar">
        @section('sidebar')	
            <p>Sidebar section from the "main" layout file.</p>
        @show
    </div>
@include('footer')
```

Blade control structures
------------------------

### Include views

```html
@include('header')
```

This command can include a view file called `header.php` or `header.blade.php`.

### Pass data to included views

```html
@include('header', ['title' => 'Documentation'])
```

### Sections

#### Overwrite section

Add the `@section` and `@endsection` statements to overwrite a layout section.

```html
@extends('layouts.main')

@section('sidebar')
    <p>New sidebar content</p>
@endsection
```

#### Extend parent section

Add the `@@parent` statement in order to keep the content of the parent section defined in the layout view.

```html
@extends('layouts.main')

@section('sidebar')
    @@parent
    <p>Child sidebar content appended to parent's content</p>
@endsection
```

### Components and slots

You can create reusable views as well by leveraging the `@component` Blade directive. Here is an example of a reusable "alert" component:

```html
<!-- /views/alert.blade.php -->
<div class="alert alert-danger">
    {{ $slot }}
</div>
```

The `{{ $slot }}` variable will contain the content you wish tin inject into the component. Now, in order to use this component, let's use the `@component` directive:

```html
@component('alert')
    <strong>Whoops!</strong> Something went wrong!
@endcomponent
```

> For more information about the component directive, please read the [Blade](https://laravel.com/docs/5.7/blade#components-and-slots) official documentation.

### Echo data

```html
Hello {{ $name }}
```
By default the double curly braces echo the data and escape it.

#### Echo unescaped data

```html
Hello {!! $name !!}
```

This is useful when you need to display HTML data like for example a post content.

#### Echoing data after checking for existence

By default you could write the following statement:

```html
{{ isset($name) ? $name : 'Default' }}
```
Instead of writing a ternary statement, Blade allows you to use PHP's built in `??` "null coalesce" operator:

```html
{{ $name ?? 'Default' }}
```

### Conditional statements
#### If

```html
@if(isset($value))
    <p>The value is {{ $value }}.</p>
@elseif(is_array($value))
    <p>The value is an array.</p>
@else
    <p>Something is wrong, there is no value.</p>
@endif
```

#### Unless

Sometimes it is more readable to use `@unless` syntax instead of `@if`.

```html
@unless(User::current()->can('edit_posts'))
    <p>No editing permission.</p>
@endunless
```

The above is the same as:

```html
@if(!User::current()->can('edit_posts'))
    <p>No editing permission.</p>
@endif
```

### Loop statements
#### For

```html
<ul>
    @for($i = 0; $i < 10; $i++)
        <li>Item {{ $i }}</li>
    @endfor
</ul>
```

#### While

```html
@while(true)
    <p>Show it!</p>
@endwhile
```

#### Foreach

```html
@foreach($items as $key => $value)
    <p>This {{ $value }} opens that {{ $key }}.</p>
@endforeach
```

> Check the [official documentation](https://laravel.com/docs/5.7/blade#the-loop-variable) for information about the `$loop` variable.

### Displaying raw text

If you need to display a string that is wrapped in curly braces, you may escape the Blade behavior by prefixing your text with an `@` symbol:

```html
@{{ This is not processed by Scout }}
```

> Check also the [verbatim](https://laravel.com/docs/5.3/blade#displaying-data) directive for larger text.

### Comments

```html
{{-- This comment will not be rendered in HTML --}}
```

### JavaScript frameworks

Since several JavaScript frameworks use curly braces to indicate expressions to be displayed in the browser, you may use the `@` symbol to inform the Blade engine to not modify the expression:

```html
Hello, @{{ name }}.
```

The above example will render the following text in your HTML so javascript can capture it for rendering:

```html
Hello, {{ name }}.
```

#### The `@verbatim` directive

If you are displaying JavaScript variables in a large portion of your template, you may wrap the HTML in the `@verbatim` directive so that you do not have to prefix each Blade echo statement with an `@` symbol:

```html
@verbatim
    <div class="container">
        Hello, {{ name }}.
    </div>
@endverbatim
```

### Stacks

Blade allows you to push to named stacks which can be rendered somewhere else in another view or layout:

```html
@push('scripts')
    <script src="/example.js"></script>
@endpush
```

You may push to a stack as many times as needed. To render the complete stack contents, pass the name of the stack to the `@stack` directive:

```html
<head>
    <!-- Head Contents -->

    @stack('scripts')
</head>
```

> More information about stacks on the official [Blade](https://laravel.com/docs/5.7/blade#stacks) documentation.

### Service injection

The `@inject` directive may be used to retrieve a service from the [service container](https://laravel.com/docs/5.7/container). The first argument passed to `@inject` is the name of the variable the service will be placed into, while the second argument is the class or interface name of the service you wish to resolve:

```html
@inject('metrics', 'App\Services\MetricsService')

<div>
    Monthly Revenue: {{ $metrics->monthlyRevenue() }}.
</div>
```

Extending Blade
---------------

Blade allows you to add new compiler statements, called directives by using the `directive()` method. The Themosis framework now provides the `Blade` facade so it is easier to extend the template engine for your application.

Here is an example where we create a `@menu` Blade directive in order to render a WordPress menu:

```php
public function boot()
{
    Blade::directive('menu', function ($expression) {
        return '<?php wp_nav_menu('.$expression.'); ?>';
    });
}
```

> We recommend you to register directives from a [service provider](https://laravel.com/docs/5.7/providers) boot method.

Themosis directives
-------------------

The Themosis framework provides extra directives to help you work with WordPress templates.

### Loop directive

The `@loop` directive gives you a shortcut to handle the WordPress loop.

In place of typing these statements:

```php
<?php
if (have_posts()) {
    while (have_posts()) {
        the_post();
?>
        <h1><?php the_title(); ?></h1>
        <div>
            <?php the_content(); ?>
        </div>
<?php
    }
}
```

Simply write this code into your view:

```html
@loop
    <h1>{{ Loop::title() }}</h1>
    <div>
        {{ Loop::content() }}
    </div>
@endloop
```

### Query directive

The `@query` directive provides a shortcut to run custom WordPress loops:

```html
@query(['post_type' => 'post', 'posts_per_page' => 3])
    <h1>{{ Loop::title() }}</h1>
    <div>
        {{ Loop::content() }}
    </div>
@endquery
```

The array you pass inside the `@query` statement is equivalent to the one you pass when using the `WP_Query` class. Check the [WordPress reference](https://developer.wordpress.org/reference/classes/wp_query/) to customize your loop query. You can also pass the WP_Query instance to the `@query` statement.

> The `Loop` class used in the examples is a core class to be used only inside the WordPress loop. More informations below.

### Head directive

The `@wp_head` directive is a shortcut for `<?php wp_head(); ?>`:

```html
<head>
    @wp_head
</head>
<body>
```

The following directives also work as alias:
- `@head`
- `@wphead`

### Footer directive

The `@wp_footer` directive is a shortcut for `<?php wp_footer(); ?>`

```html
    @wp_footer
</body>
```

The following directives also work as alias:
- `@footer`
- `@wpfooter`

### Template directive

The `@template` directive is a mechanism similar to the `get_template_part()` function for Blade views:

```html
@template('parts.content', get_post_type())
```

The above example will try to load the following views: `parts.content-products` or `parts.content-page` or  `parts.content-post`...

If the directive was not able to find one of those "composed" views, it will try to fetch the default `parts.content` view passed as the first argument.

Loop helper
-----------

The `Loop` helper class provides methods with a simple syntax in order to call WordPress loop functions.

Here is a list of the available methods.

> This class only works inside WordPress loop statements. The `Loop` class methods always return a result, so use echo statements to output their content.

### Get the ID of current post

```php
@loop
    $id = Loop::id();
@endloop
```

### Get the title of current post

This method accepts one argument:

- **$post**: _int|WP\_Post_ The post ID or WP_Post instance

```php
@loop
    <h1>{{ Loop::title() }}</h1>
@endloop
```

### Get the author

```php
@loop
    <em>{{ Loop::author() }}</em>
@endloop
```

### Get author meta

- **$field**: _string_ The author meta name
- **$user_id**: _int_ The author ID

```php
@loop
    <em>{{ Loop::authorMeta('email') }}</em>
@endloop
```

### Get the content of current post

- **$more_text**: _string_ The more text appended to the excerpt
- **$strip_teaser**: _bool_ Strip teaser content before the more text

```php
@loop
    <article>{{ Loop::content() }}</article>
@endloop
```

### Get the excerpt of current post

- **$post**: _int|WP\_Post_ The post ID or instance

```php
@loop
    <aside>{{ Loop::excerpt() }}</aside>
@endloop
```

### Get the thumbnail of current post

This method accepts two arguments:

- **$size**: _string|array_ The size of the thumbnail
- **$attr**: _string|array_ The img tag attributes
- **$post**: _int|WP\_Post_  The post ID or instance

```php
@loop
    {{ Loop::thumbnail('thumbnail') }}
@endloop
```

### Get the thumbnail URL

- **$size**: _string_ The image size
- **$icon**: _bool_ Use media icon

```php
@loop
    <img src="{{ Loop::thumbnailUrl('thumbnail') }}">
@endloop
``` 

### Get the permalink of current post

- **$post**: _int|WP\_Post_ The post ID or instance
- **$leavename**: _bool_ Keep or not the post name.

```php
@loop
    <a href="{{ Loop::link() }}">Read more</a>
@endloop
```

### Get the categories of current post

- **$id**: _int_ The post ID

```php
@loop
    <ul>
        @foreach(Loop::category() as $cat)
            <li>{{ $cat->name }}</li>
        @endforeach
    </ul>
@endloop
```

### Get the tags of current post

- **$id**: _int_ The post ID

```php
@loop
    <ul>
        @foreach(Loop::tags() as $tag)
            <li>{{ $tag->name }}</li>
        @endforeach
    </ul>
@endloop
```

### Get the custom taxonomy terms of current post

Pass the custom taxonomy slug as first argument.

- **$taxonomy**: _string_ The taxonomy slug
- **$post**: _int|WP\_Post_ The post ID or instance

```php
@loop
    <ul>
        @foreach(Loop::terms('custom-slug') as $term)
            <li>{{ $term->name }}</li>
        @endforeach
    </ul>
@endloop
```

### Get the date

- **$d**: _string_ The date format
- **$post**: _int|WP\_Post_ The post ID or instance

```php
@loop
    <time>{{ Loop::date() }}</time>
@endloop
```

### Display the class attribute of the current post

The `Loop::postClass()` method returns the HTML `class` attribute with WordPress generated class terms. You also have the opportunity to add one or more custom classes to the current post or a defined post.

**Loop::postClass($class = '', $post_id = null)**:

- **$class**: _string|array_ One or more classes to add.
- **$post_id**: _int|WP_Post_ Given post ID or post object.

Output the post classes:

```php
@loop
    <article {{ Loop::postClass() }}>
        <h2>Title</h2>
    </article>
@endloop
```

Here is an example of the rendered HTML code with the class attribute:

```html
<article class="post-4 post type-post status-publish hentry">
    <h2>Title</h2>
</article>
```

> Note that this function is heavy and may slow your pages if used during the output of a list of posts.

### Display next post link

- **$label**: _string_ Link content
- **$max_page**: _int_ Max pages in current query

```php
@loop
    {!! Loop::nextPage() !!}
@endloop
```

### Display previous post link

- **$label**: _string_ Link content

```php
@loop
    {!! Loop::previousPage() !!}
@endloop
```

### Display archive pagination

- **$args**: _string|array_ Paginate links [arguments]()

```php
@loop
    {!! Loop::paginate() !!}
@endloop
```

Poedit
------

By default, Poedit cannot find strings to translate from Blade templates. Here is a list of parameters to add to your Poedit software preferences so it can detect translation strings from your Blade views:

1. Open Poedit
2. Go to Preferences -> Parsers
3. Add a new parser with following settings:
	- Language: `Blade`
	- Extension: `*.blade.php`
	- Parser command: `xgettext --language=Python --add-comments=TRANSLATORS --force-po -o %o %C %K %F`
	- An item in keyword list: `-k%k`
	- An item in input files list: `%f`
	- Source code charset: `--from-code=%c`
4. Click save

In order for this to work, you must have a poedit project correctly setup pointing to the `languages` folder of either your theme or custom plugin and define the gettext methods and resources base path. From the catalog preferences, set the sources paths like so:

- Base path: `../`
- Paths:
    - `inc`
    - `views`

as well as adding the following gettext methods to the source keywords catalog tab:

- `__`
- `_e`
- `_n:1,2`
- `_x:1,2c`
- `_ex:1,2c`
- `_nx:4c,1,2`
- `esc_attr__`
- `esc_attr_e`
- `esc_attr_x:1,2c`
- `esc_html__`
- `esc_html_e`
- `esc_html_x:1,2c`
- `_n_noop:1,2`
- `_nx_noop:3c,1,2`
- `__ngettext_noop:1,2`
