Views
=====

1. Views
2. Scout templates



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

#### Passing datas to the view
```php
$view = View::make('welcome')->with('name', 'Julien'));
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
@include('header', array('title' => 'Documentation')
```

#### Overwrite sections
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

#### WordPress Loop
```html
@loop(array('post_type' => 'post', 'posts_per_page' => -1))
	
	<h1>{{ Loop::title() }}</h1>
	<div>
		{{ Loop::content() }}
	</div>

@endloop
```

The array you pass inside the `@loop` statement is equivalent to the one you pass when using the `WP_Query` class. Check the [WordPress codex](http://codex.wordpress.org/Class_Reference/WP_Query) to customize your loop query.

> The `Loop`class used in the example is a core class to be used only inside the WordPress Loop. More details on the `Loop` class [here](https://github.com/themosis/documentation/blob/master/loop.md).

#### Displaying raw text with curly braces

If you need to display a string that is wrapped in curly braces, you may escape the Scout behavior by prefixing your text with an @ symbol:
```html
@{{ This is not processed by Scout }}
```

#### Comments
```html
{{-- This comment will not be rendered in HTML --}}
```

Next
----
Read the [controllers guide](https://github.com/themosis/documentation/blob/master/controllers.md)