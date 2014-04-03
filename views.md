Views
=====

1. Views
2. Templates

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
Route::is('home', function(){

	return View::make('welcome', array('name' => 'Julien'));

});
```

The second argument is an array of data available to the view.

#### Passing datas to the view
```php
$view = View::make('welcome')->with(array('name' => 'Julien'));
```

Or you can pass a `$datas` array as a second parameter in the `make` method like so:
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
Route::is('home', function(){

	return View::make('pages.home');

});
```
> There is no limitation to the number of sub-folders you want to use to organize your views.

2. Templates
------------
Themosis framework is bundled with a templating engine called `Scout`. The engine provides helpers in order to rapidly build your view.

In order to use the Scout engine, all your template/view files should use the `.scout.php` extension.

#### Basic Scout view
```html
<!-- View stored in app/views/welcome.scout.php -->
@include('header')
	<h1>Hello, {{ $name }}</h1>
@include('footer')
```

And this view may be returned to the browser like so:
```php
Route::is('home', function(){

	return View::make('welcome', array('name' => 'Julien'));

});
```

In the previous example, we used the `@include` syntax. This function allows you to include `.php` view files that are located in the root of the `app/views` directory. **Note:** The `include` function do not work with view files with an extension of `.scout.php` at the moment and you can't organize them into sub-folders. But these features will be available on a later version.

### Scout control structures

#### Include views
```php
@include('header')
```
> This will include into your view the `header.php` file stored inside the root of your `app/views` directory.

#### Echo data
```php
Hello {{ $name }}
```
> The double curly braces echo the data but do not escape it.

#### If statements
```php
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
```php
@for($i = 0; $i < 10; $i++)

	<ul>
		<li>Item {{ $i }}</li>
	</ul>

@endfor
```

##### While
```php
@while(true)

	<p>Show it!</p>

@endwhile
```

##### Foreach
```php
@foreach($objects as $key => $value)

	<p>This {{ $value }} opens that {{ $key }}.</p>

@endforeach
```

#### WordPress Loop
```php
@loop(array('post_type' => 'post', 'posts_per_page' => -1))
	
	<h1>{{ Loop::title() }}</h1>
	<div>
		{{ Loop::content() }}
	</div>

@endloop
```

The array you pass inside the `@loop` statement is equivalent to the one you pass when using the `WP_Query` class. Check the [WordPress codex](http://codex.wordpress.org/Class_Reference/WP_Query) to customize your loop query.

> The `Loop`class used in the example is a core class to be used only inside the WordPress Loop. More details on the `Loop` class [here](https://github.com/themosis/documentation/blob/master/loop.md).

Next
----
Check the [controllers guide](https://github.com/themosis/documentation/blob/master/controllers.md)