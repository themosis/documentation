Views
=====
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