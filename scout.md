Scout templates
===============

- Basic usage
- Scout control structures
	- Include views
	- Pass data to included views
	- Sections
	- Echoing data
	- Conditional statements
	- Loop statements
	- WordPress loop
	- Displaying raw text
	- Comments
- Loop helper

Basic usage
-----------

Themosis framework is bundled with a templating engine called `Scout`. The engine is a fork of the Laravel Blade engine and provides helpers in order to rapidly build your view.

In order to use the Scout engine, all your template/view files should use the `.scout.php` extension.

Here is an example of a basic view stored inside the `resources/views` folder:

```html
<!-- View stored in resources/views/welcome.scout.php -->
@extends('layouts.main')

@section('main')
	<h1>Hello, {{ $name }}</h1>
@stop

@section('sidebar')
	<h3>Latest posts</h3>
	<ul>
		<li>First post</li>
		<li>Second post</li>
	</ul>
@stop
```

And this view may be returned to the browser like so:

```php
Route::get('home', function()
{
	return View::make('welcome', ['name' => 'Julien']);
});
```

In the previous example, we used the `@extends` syntax. This function allows you to use layouts:

```html
<!-- Layout stored in resources/views/layouts/main.scout.php -->
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

Scout control structures
------------------------

### Include views

```html
@include('header')
```

This command can include a view file called `header.php` or `header.scout.php` stored inside the root of the `resources/views` directory.

### Pass data to included views

```html
@include('header', ['title' => 'Documentation'])
```

### Sections

#### Overwrite section

Add the `@overwrite` statement when closing your section. This will overwrite the content of the parent section defined inside the layout view.

```html
@extends('layouts.main')

@section('sidebar')
	<p>New sidebar content</p>
@overwrite
```

#### Extend parent section

Add the `@parent` statement in order to keep the content of the parent section defined in the layout view.

```html
@extends('layouts.main')

@section('sidebar')
    @parent
    <p>Child sidebar content appended to parent's content</p>
@stop
```

### Echoing data

```html
Hello {{ $name }}
```
The double curly braces echo the data but do not escape it.

#### Echo and escape data

```html
Hello {{{ $name }}}
```
Use three curly braces to escape your data. This command pass your data through the PHP function `htmlentities()`.

#### Echoing data after checking for existence

By default you could write the following statement:

```html
{{{ isset($name) ? $name : 'Default' }}}
```
Instead of writing a ternary statement, Scout allows you to use the following convenient short-cut:

```html
{{{ $name or 'Default' }}}
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
@if( ! User::current()->can('edit_posts'))
	<p>No editing permission.</p>
@endunless
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
@foreach($objects as $key => $value)
	<p>This {{ $value }} opens that {{ $key }}.</p>
@endforeach
```

### WordPress loop

The Scout engine gives you a shortcut to use the WordPress loop.

In place of typing these statements:

```php
<?php
if (have_posts())
{
	while(have_posts())
	{
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

Simply write this code inside your view:

```html
@loop
	<h1>{{ Loop::title() }}</h1>
	<div>
		{{ Loop::content() }}
	</div>
@endloop
```

#### Custom WordPress Query

The Scout engine also provides a helper to make custom loops:

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

### Displaying raw text

If you need to display a string that is wrapped in curly braces, you may escape the Scout behavior by prefixing your text with an `@` symbol:

```html
@{{ This is not processed by Scout }}
```

### Comments

```html
{{-- This comment will not be rendered in HTML --}}
```

Loop helper
-----------

The `Loop` helper class provides methods with a simple syntax in order to call WordPress loop functions.

Here is a list of the available methods.

> Currently this class only works inside WordPress loop statements. The `Loop` methods always return a result, so use echo statements to output their content.

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

Next
----
This article closes the "Getting started" guide.

There are many APIs that will help you shape your WordPress administration. Discover those features/API of the framework below:

* [Action](http://framework.themosis.com/docs/action/)
* [Ajax](http://framework.themosis.com/docs/ajax/)
* [Asset](http://framework.themosis.com/docs/asset/)
* [Configuration](http://framework.themosis.com/docs/configuration/)
* [PostType](http://framework.themosis.com/docs/posttype/)
* [Form](http://framework.themosis.com/docs/form/)
* [Field](http://framework.themosis.com/docs/field/)
* [Meta](http://framework.themosis.com/docs/meta/)
* [Metabox](http://framework.themosis.com/docs/metabox/)
* [Taxonomy](http://framework.themosis.com/docs/taxonomy/)
* [Page](http://framework.themosis.com/docs/page/)
* [Option](http://framework.themosis.com/docs/option/)
* [User](http://framework.themosis.com/docs/user/)
* [Validation and input](http://framework.themosis.com/docs/validation/)
* [Widgets](http://framework.themosis.com/docs/widgets/)
* [Helpers](http://framework.themosis.com/docs/helpers/)
* [WP-CLI](http://framework.themosis.com/docs/wp-cli/)