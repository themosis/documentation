Views
=====

1. Views
2. Scout templates
3. Loop class helper

1. Views
--------

Views contain the HTML of your website/application. They provide a convenient way of separating your controller and domain logic from your presentation logic. Views are stored in the `app/views` directory of your `themosis-theme` theme.

Here is an example of a simple view:
```html
<!-- View stored in app/views/welcome.php -->
<html>
	<head>
		<title>View example</title>
	</head>
	<body>
		<h1>Hello, <?php echo $name; ?></h1>
	</body>
</html>
```

And this view may be returned to the browser like so:
```php
Route::get('home', function(){

	return View::make('welcome', array('name' => 'Julien'));

});
```

The second argument is an array of data available to the view.

#### Passing data to the view

The `with($key, $value)` method allows you to pass data to your view:

- **$key**: _string|array_ A key name or an array of key => value pair.
- **$value**: _mixed_ The value of your key variable.

```php
// The 'welcome' view has the $name variable with the value of 'Julien'.
$view = View::make('welcome')->with('name', 'Julien');

// Same method but by passing an array inside the with method.
$view = View::make('welcome')->with(array(
	'name'		=> 'Julien',
	'foo'		=> 'Bar'
));
```

Or you can pass a `$data` array as a second parameter in the `make` method like so:

```php
$view = View::make('welcome', array('name' => 'Julien'));
```

#### Organizing your views

You can organize your views into sub-folders and access them using a dot syntax.

```html
<!-- This view is stored in app/views/pages/home.scout.php -->
<html>
	<head>
		<title>Welcome</title>
	</head>
	<body>
		<p>This is the home page</p>
	</body>
</html>
```

This view may be returned to the browser like so:

```php
Route::get('home', function(){

	return View::make('pages.home');

});
```
> There is no limitation to the number of sub-folders you want to use to organize your views.

#### Share data across all views

The `View::share($key, $value)` method allows you to share data across all used views of your WordPress application.

* **$key**: _string|array_ An array of key => value or simply the key name of your data. The key will be available as a variable inside your views. Take care to not overwrite existing ones.
* **$value**: _mixed_ The value associated to your key.

```php
// Single data shared across views.
// The $user variable with a value of 'FooBar' is available in all views.
View::share('user', 'FooBar');

// Multiple data shared across views.
View::share(array(
	'user'		=> 'FooBar',
	'blog'		=> 'WordPress'
));
```

#### View composers

The `View::composer($views, $callback)` and `View::composers($composers)` methods allows to run a callback or class instance method when a specific view is rendered.

**View::composer($views, $callback):**
- **$views**: _string|array_ One or more views to listen to.
- **$callback**: _Closure|string The callback or class method to run when the defined views are called.

**View::composers($composers):**
- **$composers**: _array_ A list of composers to register/run.

Example of one view composer run when the home page view is called:

```php
// Look at the home page view used on home page URL
Route::get('home', function()
{
	// The view name is 'pages.home'.
	return View::make('pages.home');
});

// Now the following code could be written inside the admin/ folder
// or inside a controller class.
View::composer('pages.home', function($view)
{
	// Pass data to the view.
	$view->with('foo', 'bar');
});
```

You can register one callback to run on multiple views like so:

```php
View::composer(array('pages.home', 'pages.contact'), function($view)
{
	// Pass the same data to the pages.home and pages.contact views.
	$view->with('foo', 'bar');
});
```

In place of using a closure, you can also tell the method to look after a class method instance like so:

```php
// Register a class and load it using the 'loading.config.php` file.
class MyComposerClass
{
	public function add($view)
	{
		// Pass data to the view.
		$view->with('foo', 'Bar');
	}

	// Default method used for a view composer.
	public function compose($view)
	{
	}
}

// The view composer.
// Use same syntax as for controllers.
View::composer('pages.home', 'MyComposerClass@add');
```

You can omit the method name when using a class instance as your composer. By default, the API is looking after a `compose` then a `create` method if the first one do not exists.

```php
// Omit the method name.
View::composer('pages.home', 'MyComposerClass');
```

You can also define multiple composers using the `View::composers` method like so:

```php
View::composers(array(
	'MyComposerClass'		=> 'pages.home',
	'MyComposerClass@add'	=> 'pages.add',
	'OtherComposerClass'	=> array('pages.home', 'pages.product')
));
```

2. Scout templates
------------------

Themosis framework is bundled with a templating engine called `Scout`. The engine is a fork of the Laravel Blade engine and provides helpers in order to rapidly build your view.

In order to use the Scout engine, all your template/view files should use the `.scout.php` extension.

#### Basic Scout view
```html
<!-- View stored in app/views/welcome.scout.php -->
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
Route::get('home', function(){

	return View::make('welcome', array('name' => 'Julien'));

});
```

In the previous example, we used the `@extends` syntax. This function allows you to use layouts:

```html
<!-- Layout stored in app/views/layouts/main.scout.php -->
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

### Scout control structures

#### Include views
```html
@include('header')
```
> This will include into your view the `header.php` or `header.scout.php` file stored inside the root of your `app/views` directory.

#### Pass data to included views
```html
@include('header', array('title' => 'Documentation'))
```

#### Overwrite sections

Add the `@overwrite` statement when closing your section. This will overwrite the content of the parent section.
```html
@extends('layouts.main')

@section('sidebar')

	<p>New sidebar content</p>

@overwrite
```

#### Echo data
```html
Hello {{ $name }}
```
> The double curly braces echo the data but do not escape it.

#### Echo and escape data
```html
Hello {{{ $name }}}
```

#### Echoing data after checking for existence

By default you could write the following statement:
```html
{{{ isset($name) ? $name : 'Default' }}}
```
Instead of writing a ternary statement, Scout allows you to use the following convenient short-cut:

```html
{{{ $name or 'Default' }}}
```

#### If statements
```html
@if(isset($value))

	<p>The value is {{ $value }}.</p>

@elseif(is_array($value))

	<p>The value is an array.</p>

@else

	<p>Something is wrong, there is no value.</p>

@endif
```

##### Unless

Sometimes it is more readable to use `@unless` syntax instead of `@if`.
```html
@unless(User::current()->can('edit_posts'))
	<p>No editing permission.</p>
@endunless
```

The above is same as:
```html
@if( ! User::current()->can('edit_posts'))
	<p>No editing permission.</p>
@endunless
```

#### Loop statements
##### For
```html
<ul>

	@for($i = 0; $i < 10; $i++)
	
		<li>Item {{ $i }}</li>
	
	@endfor

</ul>
```

##### While
```html
@while(true)

	<p>Show it!</p>

@endwhile
```

##### Foreach
```html
@foreach($objects as $key => $value)

	<p>This {{ $value }} opens that {{ $key }}.</p>

@endforeach
```

#### The WordPress Loop

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
Simply write this code in your view:

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
@query(array('post_type' => 'post', 'posts_per_page' => 3))
	
	<h1>{{ Loop::title() }}</h1>
	<div>
		{{ Loop::content() }}
	</div>

@endquery
```

The array you pass inside the `@query` statement is equivalent to the one you pass when using the `WP_Query` class. Check the [WordPress codex](http://codex.wordpress.org/Class_Reference/WP_Query) to customize your loop query.

> The `Loop` class used in the examples is a core class to be used only inside the WordPress loop. More informations below.

#### Displaying raw text with curly braces

If you need to display a string that is wrapped in curly braces, you may escape the Scout behavior by prefixing your text with an @ symbol:
```html
@{{ This is not processed by Scout }}
```

#### Comments
```html
{{-- This comment will not be rendered in HTML --}}
```

3. Loop
-------

The `Loop` helper class provides methods with a simple syntax in order to call WordPress loop functions.

Here is a list of the available methods.

> Currently this class only works inside WordPress loop statements. The `Loop` methods always return a result, so use echo statements to output their content.

#### Get the ID of current post

```php
@loop

    $id = Loop::id();

@endloop
```

#### Get the title of current post

```php
@loop

	<h1>{{ Loop::title() }}</h1>

@endloop
```

#### Get the content of current post

```php
@loop

	<article>{{ Loop::content() }}</article>

@endloop
```

#### Get the excerpt of current post

```php
@loop

	<aside>{{ Loop::excerpt() }}</aside>

@endloop
```

#### Get the thumbnail of current post

This method accepts two arguments:

- **$size**: _string|array_ The size of the thumbnail
- **$attr**: _string|array_ The img tag attributes

```php
@loop

	{{ Loop::thumbnail('thumbnail') }}

@endloop
```

#### Get the permalink of current post

```php
@loop

	<a href="{{ Loop::link() }}">Read more</a>

@endloop
```

#### Get the categories of current post

```php
@loop

    <ul>
        @foreach(Loop::category() as $cat)
            <li>{{ $cat->name }}</li>
        @endforeach
    </ul>

@endloop
```

#### Get the tags of current post

```php
@loop

    <ul>
        @foreach(Loop::tags() as $tag)
            <li>{{ $tag->name }}</li>
        @endforeach
    </ul>

@endloop
```

#### Get the custom taxonomy terms of current post

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

#### Display the class attribute of the current post

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
Read the [controllers guide](http://framework.themosis.com/docs/controllers/)
