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
- [Themosis Twig Extensions](#themosis-twig-extensions)

Introduction
------------

The Themosis framework now adds support to the Twig template engine. For a more detailed presentation of the Twig engine, please check the official documentation:

- [Twig official documentation](http://twig.sensiolabs.org/)

In this documentation, you'll find the basics operations on how to get started with Twig engine as well as the Twig extensions the Themosis framework has added in order to help you work with WordPress and its core functions within your views. For more advanced topics regarding Twig, please refer to the official documentation.

Basic usage
-----------

In order to use the Blade engine, all your view files should use the `.blade.php` extension.

> Previous versions of the Themosis framework used a fork of the Blade engine called Scout. It is now deprecated to use it. For backward compatibility, views using the `.scout.php` file extension are now compiled through the Blade engine. But you'll need to update view statements as `{{ }}` double curly braces now escape data causing some layout issues during upgrade.

Here is an example of a basic view, using Blade, stored inside the `resources/views` folder:

```html
<!-- View stored in resources/views/welcome.blade.php -->
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
Route::get('home', function ($post, $query) {
    $posts = $query->get_posts();
    return view('welcome', ['name' => 'Julien', 'items' => $posts]);
});
```

In the previous example, we used the `@extends` syntax. This function allows you to use layouts:

```html
<!-- Layout stored in resources/views/layouts/main.blade.php -->
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

Add the `@endsection` statement when closing your section. This will overwrite the content of the parent section defined inside the layout view.

```html
@extends('layouts.main')

@section('sidebar')
    <p>New sidebar content</p>
@endsection
```

#### Extend parent section

Add the `@parent` statement in order to keep the content of the parent section defined in the layout view.

```html
@extends('layouts.main')

@section('sidebar')
    @parent
    <p>Child sidebar content appended to parent's content</p>
@endsection
```

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
Instead of writing a ternary statement, Blade allows you to use the following convenient short-cut:

```html
{{ $name or 'Default' }}
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

> Check the [official documentation](https://laravel.com/docs/5.3/blade#the-loop-variable) for information about the `$loop` variable.

### Displaying raw text

If you need to display a string that is wrapped in curly braces, you may escape the Scout behavior by prefixing your text with an `@` symbol:

```html
@{{ This is not processed by Scout }}
```

### Comments

```html
{{-- This comment will not be rendered in HTML --}}
```

Extending Blade
---------------

Blade allows you to add new compiler statements, called directives by using the `directive()` method. The Themosis framework already extends Blade but do not provide a "facade" to access it like in the official documentation.

In order to get the Blade compiler instance, use the following code:

```php
$blade = container('view')->getEngineResolver()->resolve('blade')->getCompiler();
```

> We recommend you to register directives from a service provider. Check the [Service provider guide]({{url}}/service) for more information.

Here is an example in order to add a simple directive `@menu()` that works as a shortcut to the WordPress function `wp_nav_menu()`.

```php
$blade = container('view')->getEngineResolver()->resolve('blade')->getCompiler();

$blade->directive('menu', function ($expression) {
    return '<?php wp_nav_menu('.$expression.'); ?>';
});
```

Themosis directives
-------------------

The Themosis framework provides extra directives to help you work with WordPress templates.

### @loop

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

### @query

The `@query` directive provides a shortcut to run custom WordPress loops:

```html
@query(['post_type' => 'post', 'posts_per_page' => 3])
    <h1>{{ Loop::title() }}</h1>
    <div>
        {{ Loop::content() }}
    </div>
@endquery
```

The array you pass inside the `@query` statement is equivalent to the one you pass when using the `WP_Query` class. Check the [WordPress codex](http://codex.wordpress.org/Class_Reference/WP_Query) to customize your loop query. You can also pass the WP_Query instance to the `@query` statement.

> The `Loop` class used in the examples is a core class to be used only inside the WordPress loop. More informations below.

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

```php
@loop
    <em>{{ Loop::authorMeta('email') }}</em>
@endloop
```

### Get the content of current post

```php
@loop
    <article>{{ Loop::content() }}</article>
@endloop
```

### Get the excerpt of current post

```php
@loop
    <aside>{{ Loop::excerpt() }}</aside>
@endloop
```

### Get the thumbnail of current post

This method accepts two arguments:

- **$size**: _string|array_ The size of the thumbnail
- **$attr**: _string|array_ The img tag attributes

```php
@loop
    {{ Loop::thumbnail('thumbnail') }}
@endloop
```

### Get the thumbnail URL

You can also pass a `$size` value (string or array) and `$icon` boolean value as arguments:

```php
@loop
    <img src="{{ Loop::thumbnailUrl('thumbnail') }}">
@endloop
``` 

### Get the permalink of current post

```php
@loop
    <a href="{{ Loop::link() }}">Read more</a>
@endloop
```

### Get the categories of current post

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

- **$taxonomy**: _string_ The taxonomy slug.

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

You can pass a date format string as an argument as well.

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

```php
@loop
    {!! Loop::nextPage() !!}
@endloop
```


### Display previous post link

```php
@loop
    {!! Loop::previousPage() !!}
@endloop
```


### Display archive pagination

```php
@loop
    {!! Loop::paginate() !!}
@endloop
```
