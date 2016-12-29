Views
=====

- [Introduction](#introduction)
- [View API](#view-api)
    - [Create a view](#create-a-view)
    - [Organize your views](#organize-your-views)
    - [Passing data](#passing-data)
	- [Sharing data](#sharing-data)
	- [Render](#render)
	- [View composers](#view-composers)
- [Blade engine](#blade-engine)
- [Twig engine](#twig-engine)

Introduction
------------

In order to handle the application views (HTML), the Themosis framework comes with a view API that extends the illuminate/view package. Beside providing same API found in the Laravel framework, the Themosis framework views come with 2 great template engines:

- [Blade](https://laravel.com/docs/5.3/blade)
- [Twig](http://twig.sensiolabs.org/)

Depending of your background, you might prefer working with the Twig engine, Blade engine or vanilla PHP, the choice is yours. Regarding the view API, there is one difference to note regarding view composers if you decide to go with the Twig engine (and vanilla PHP as well). See view composers below for the details.

> In versions anterior to 1.3.0, there was the Scout template engine which was a fork of the Blade engine. The Scout template were using a `.scout.php` file extension. For backward compatibility, you can still use this file extension but it is recommended to rename your files with a `.blade.php` file extension instead.

View API
--------

Views contain the HTML of your website/application. They provide a convenient way of separating your controllers and application logic from your presentation logic.

Before digging into the view API available methods, let's take a look on how views are loaded.

Views are registered through the use of Views Finder class. By default, views are stored in the `resources/views` directory of either your `themosis-theme` theme or custom plugin.

If you need to add more views directories (locations) in your project, you can do so by fetching the finder instance and call its `addLocation()` method like so:

```php
container('view.finder')->addLocation($path_to_directory);
```

> In previous code sample, we use the helper function `container()` which resolve a view finder instance. Check the [service container guide]({{url}}/container) for more information.

Here is an example of a simple view:

```html
<!-- View stored in resources/views/welcome.php -->
<html>
    <head>
        <title>Welcome WordPress developers</title>
    </head>
    <body>
        <h1>Hello, <?php echo $name; ?></h1>
    </body>
</html>
```

And this view may be returned to the browser like so:

```php
Route::get('home', function()
{
    return View::make('welcome', ['name' => 'Julien']);
});
```

As you can see, the first argument passed to the `View::make()` method is the name of the view file stored in the `resources/views/` directory. The second argument is an array of data available to the view. So inside the view file, you can access the `$name` variable with a value of `Julien` in this example.

### Organize your views

You can organize your views into sub-directories of the `resources/views` directory. Use the "dot" notation to reference your view. For example, if you store a view file at `resources/views/pages/home.php`, you can access it like so:

```php
return View::make('pages.home');
```

> There is no limitation to the number of sub-folders you want to use to organize your views.

### Passing data to views

As you saw in previous code examples, you can send data to a view by passing an array as a second argument to the `View::make()` method:

```php
return View::make('home', ['name' => 'Joe']);
```

When using this method, the array should be with key/value pairs. Then, inside your view, you can access each value using its corresponding key like so:

```php
<p><?php echo($name); ?></p>
```

As an alternative, you can also use the `with()` method to pass individual pieces of data to the view:

```php
$view = View::make('welcome')->with('name', 'Joe');
```

> Note: the `with()` method can also accept data as an array with key/value pairs.


### Sharing data with all views

The `View::share()` method allows you to share data across all views of your WordPress application. The method accepts an array with key/value pairs or individual piece of data.

```php
// Multiple data shared across views.
View::share([
    'key'  => 'value',
    'blog' => 'WordPress'
]);

// Single data shared across views.
View::share('key', 'value');
```

### Render your views

Views are not reserved to your page templates only. In general, a view is compiled and is returned as a string. For example, you could create a view for an email, sending form data to it, render the view and return the compiled string as the email's message.

To compile your custom views, simply use the `render()` method like so:

```php
$message = View::make('emails.message', ['data' => $data])->render();

// Use wp_mail function to send your email with your compiled view as the message
$sent = wp_mail($to, 'Subject', $message);
```

View composers
--------------

The `View::composer($views, $callback)` and `View::composers($composers)` methods allows you to run a callback or class instance method when a specific view is rendered. If you have data that you want to be bound to a view each time that view is rendered, a view composer can help you organize that logic into a single location.

Here is an example of a view composer that runs when the `pages.home` view is called:

```php
// The following code could be written inside the admin/ folder
// or inside a controller class.
View::composer('pages.home', function($view)
{
    // Pass data to the view.
    $view->with('foo', 'bar');
});

// routes.php
Route::get('home', function()
{
    // Each time this view is rendered,
    // the registered view composer is called right before.
    return View::make('pages.home');
});
```

You can register one callback to run on multiple views like so:

```php
View::composer(['pages.home', 'pages.contact'], function($view)
{
    // Pass the same data to the pages.home and pages.contact views.
    $view->with('foo', 'bar');
});
```

In place of using a closure, you can also tell the method to look after a class method like so:

```php
// Register a class
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
View::composers([
    'MyComposerClass'     => 'pages.home',
    'MyComposerClass@add' => 'pages.add',
    'OtherComposerClass'  => ['pages.home', 'pages.product']
]);
```

Next
----
Read the [Scout template guide]({{url}}/scout)
