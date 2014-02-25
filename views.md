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


Next
----
Check the [controllers guide](https://github.com/themosis/documentation/blob/master/routing.md)