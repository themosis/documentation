Views
=====

- [Introduction](#introduction)
- [Create a view](#create-a-view)
- [Render a view](#render-a-view)
- [Organize your views](#organize-your-views)
- [Passing data](#passing-data)
- [Sharing data](#sharing-data)
- [View composers](#view-composers)
- [Template engines](#template-engines)

Introduction
------------

In order to handle the application views (HTML), the Themosis framework comes with a view API that extends the `illuminate/view` package. Beside providing same API found in the Laravel framework, the Themosis framework views come with 2 great template engines:

- [Blade](https://laravel.com/docs/5.7/blade)
- [Twig](https://twig.symfony.com/)

Depending on your background, you might prefer working with the Twig engine, Blade engine or vanilla PHP, the choice is yours. Regarding the view API, there is one difference to note regarding view composers if you decide to go with the Twig engine (and vanilla PHP as well). See view composers below for the details.

Views contain the HTML of your application. They provide a convenient way of separating your controllers and application logic from your presentation logic.

Before digging into the view API available methods, let's take a look on how views are loaded.

Views are registered through the use of a finder class. By default, views are stored in the `views` directory of your theme and the `resources/views` directory located at project root.

If you need to add more views directories (locations) in your project, you can do so by editing the `config/theme.php` file from your theme. Or if your need to add more global views directories, use the `config/view.php` configuration file from project root.

Create a view
-------------

The most basic view is a PHP file. To create a view, first add a new PHP file into one of the views directories. Here is an example of a simple view file stored as `hello.php`:

```html
<!-- View stored in views/hello.php -->
<html>
    <head>
        <title>Welcome</title>
    </head>
    <body>
        <h1>Hello, World!</h1>
    </body>
</html>
```

Then, in order to create a view instance from this view file, call the `make()` method and pass it our `hello` view name like so:

```php
$hello = View::make('hello');
```

The code above only creates a view instance and do not render anything on screen. The first parameter you pass to the `make()`method is the view name, which is basically the file name (or path) without the its file extension.

Render a view
-------------

Now that we have a view instance, let's render it and return the output to the user from a route. In order to render our previously defined `hello` view, you'll call the `render()` method on it. The render method compiles the view and return it's content as a string.

```php
$hello = View::make('hello')->render();

// Output view content.
echo $hello;
```

The previous sample is a basic representation of a view rendering. Let's use this view from a route and return its content:

```php
Route::get('/', function () {
    return View::make('hello');
});
```

> Notice that we don't need to call the `render()` method when returning a view instance from a route.

You can also call the `render()` method and return the compiled view string like so:

```php
Route::get('home', function () {
    return View::make('hello')->render();
});
```

> The advantage of rendering the view before returning it is that if your view code has an error, you'll get an appropriate PHP stack error spotting the right line of code.

Finally, the Themosis framework also includes the `view()` helper function that saves you some typos. Here is our `hello` example using the `view()` function:

```php
Route::get('home', function () {
    return view('hello');
});
```

> It is recommended to use the `view()` helper function in place of the long form.

Finally views are not only reserved to page templates. Views help you write better presentation code without mixing too much PHP logic with your HTML. From a WordPress perspective, there are many places where you could use views, here is a helper list:

- Metabox
- Fields
- Widgets
- Shortcodes
- List table
- Email
- ...

...anything that contains HTML.

Here is an example on using a view to build an email body content:

```php
$message = view('emails.message');

// Use wp_mail function to send your email with our compiled view
wp_mail($to, 'Subject', $message);
```

Organize your views
-------------------

You can organize your views into sub-directories of the `views` directory. Use the "dot" notation to reference your view. For example, if you store a view file at `views/pages/home.php`, you can access it like so:

```php
return view('pages.home');
```

There is no limitation to the number of sub-folders you want to use in order to organize your views.

> Important, if you have multiple view directories shared between your theme and plugins, the view API will use the first view file it found. Meaning that if a plugin has a view stored exactly with the same relative path from its view directory, you may get inappropriate rendering. It is recommended to store plugin views with a higher folder hierarchy. For example, use your company TLD + Domain Name + Plugin Name as sub-directories names to avoid conflicts.

Passing data
------------

Based on our previous example, let's pass data to our `hello.php` view file. In order to send data to a view, pass an associative array as a second argument to the `view()` helper function like so:

```php
return view('hello', ['name' => 'Joe']);
```

And now let's update our `hello.php` view file to take advantage of the passed `$name` variable:

```php
<!-- View stored in views/hello.php -->
<html>
    <head>
        <title>Welcome</title>
    </head>
    <body>
        <h1>Hello, <?php echo $name; ?>!</h1>
    </body>
</html>
```

The updated view will now display the "Hello, Joe!" text in the browser.

When passing data, always pass an array with key/value pairs. Then, inside your view, you can access each value using its corresponding key like in the previous example:

```php
<p><?php echo $name; ?></p>
```

As an alternative, you can also use the `with()` method to pass individual or multiple pieces of data to the view:

```php
// Passing one variable
$view = View::make('hello')->with('name', 'Joe')->render();

// Passing multiple variables
$view = View::make('hello')->with(['name' => 'Joe', 'age' => 30])->render();
```

Finally, the view `make()` method also accepts variables as a second argument. Here is an example:

```php
$view = View::make('hello', ['name' => 'Joe']);
```

Sharing data
------------

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

View composers
--------------

The `View::composer($views, $callback)` and `View::composers($composers)` methods allow you to run a callback or class instance method when a specific view is rendered. If you have data that you want to be bound to a view each time that view is rendered, a view composer can help you organize that logic into a single location.

> If you're using the Blade engine for your template, the `@include` statement also triggers view composers.

Here is an example of a view composer that runs when the `pages.home` view is called:

```php
View::composer('pages.home', function ($view) {
    // Pass data to the view.
    $view->with('foo', 'bar');
});

// routes.php
Route::get('home', function () {
    // Each time this view is rendered,
    // the registered view composer is called right before.
    return view('pages.home');
});
```

You can register one callback to run on multiple views like so:

```php
View::composer(['pages.home', 'hello'], function ($view) {
    // Pass the same data to the pages.home and hello views.
    $view->with('foo', 'bar');
});
```

In place of using a closure, you can also tell the method to look after a class method like so:

```php
<?php

namespace Theme\Library;

class MyComposer
{
    public function add ($view)
    {
        // Pass data to the view.
        $view->with('foo', 'Bar');
    }

    // Default method used for a view composer.
    public function compose ($view)
    {
    }
}

// The view composer.
// Use same syntax as for controllers excepts that you need to pass
// the full class name.
View::composer('pages.home', 'Theme\Library\MyComposer@add');
```

You can omit the method name when using a class instance as your composer. By default, the API is looking after a `compose` then a `create` method if the first one do not exists.

```php
// Omit the method name.
View::composer('pages.home', 'Theme\Library\MyComposer');
```

You can also define multiple composers using the `View::composers` method like so:

```php
View::composers([
    'Theme\Library\MyComposer' => 'pages.home',
    'Theme\Library\MyComposer@hello' => 'hello',
    'Theme\Library\OtherComposer' => ['pages.home', 'pages.product']
]);
```

Template engines
----------------

As described in the introduction, the Themosis framework comes with 2 great template engines: Blade and Twig. We recommend you to stick with one template engine per theme and per plugin.

We have a guide for each template engine covering their basics and specific features added by the Themosis framework:

- [Blade template guide]({{url}}/blade)
- [Twig template guide]({{url}}/twig)

but for deeper information, we suggest you to read their official documentation:

- [Blade](https://laravel.com/docs/5.7/blade)
- [Twig](https://twig.symfony.com/)

Next
----
Read the [Blade]({{url}}/blade) or [Twig]({{url}}/twig) template guide.