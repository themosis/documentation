Errors handling
=============

- [Introduction](#introduction)
- [Configuration](#configuration)
- [WordPress](#wordpress)
- [The exception handler](#the-exception-handler)
    - [Report](#report)
    - [Render](#render)
- [Reportable & renderable exceptions](#reportable-renderable-exceptions)
- [HTTP exceptions](#http-exceptions)
- [Custom HTTP error pages](#custom-http-error-pages)

Introduction
------------

The Themosis framework is already configured to handle errors and exceptions. The `App\Exceptions\Handler` class is where all exceptions triggered by your application are logged and then rendered back to the user.

By default, the framework reports all exceptions to the `themosis.log` file stored into the `storage/logs` directory.

Configuration
-------------

The `debug` option in your `config/app.php` configuration file determines if the information is rendered to the user or not. By default, this option is set to respect the value of the `APP_DEBUG` environment variable stored in your `.env` file.

For local development, the `APP_DEBUG` environment variable is by default configured and set to `true`. On production, it is recommended to always set the value to `false`. Leaving a `true` value on a production environment may expose sensitive information to your application's end users.

WordPress
---------

When switching from a classic WordPress development environment to the Themosis framework, developers are not used to get full report on any errors triggered by a WordPress application.

During development, you may encounter WordPress plugins "incompatibility" because the framework is triggering an exception. It is not "incompatible" but the process is blocked due to the default error reporting. Setting the `APP_DEBUG` value to `false` may fix your issue in general but it is a bad practice to disable error reporting on local development as you may let other errors pass through just for plugin compatibility.

We suggest you:
- to report the error to the plugin author
- to make a fork of it and fix it if available

The exception handler
---------------------

All exceptions are handled by the `App\Exceptions\Handler` class. The class contains two methods: `report` and `render`.

### Report

The `report` method is used to log exceptions or send them to an external service like [Bugsnag](#https://www.bugsnag.com/) or [Sentry](https://sentry.io/). By default, the `report` method passes the exception to the base class where the exception is logged. However, you are free to log exceptions in any way you want by adding the `report` method to the `App\Exceptions\Handler` class like so:

```php
/**
 * Report or log an exception.
 * 
 * @param \Exception $exception
 */
public function report(Exception $exception)
{
    if ($exception instanceof WordPressException)
    {
        //
    }
    
    parent::report($exception);
}
```

> If you have many different exceptions to check, it is best to use [reportable exceptions](#reportable-exceptions)

#### Global log context

If available, the framework automatically adds the current customer's ID to every exception's log message as contextual data. You may define your own global contextual data by overriding the `context` method of your application's `App\Exceptions\Handler` class. The information will be included in every exception's log message written by your application:

```php
protected function context()
{
    return array_merge(parent::context(), [
        'foo' => 'bar'
    ]);
}
```

#### The report helper

Sometimes you may need to report an exception but continue handling the current request. The `report` helper function allows you to quickly report an exception using your exception handler's `report` method without rendering an error page:

```php
public function isValid()
{
    try {
        // Validate some value...
    } catch (Exception $e) {
        report($e);
        
        return false;
    }
}
```

#### Ignoring exceptions by type

The `$dontReport` property of the exception handler allows you to define an array of exception types to not log. For example, exceptions resulting in 404 errors are not written to your log files. You may add other exceptions types to this array as needed:

```php
protected $dontReport = [
    AuthenticationException::class,
    AuthorizationException::class,
    HttpException::class,
    HttpResponseException::class,
    ModelNotFoundException::class,
    TokenMismatchException::class,
    ValidationException::class
];
```

### Render

The `render` method is responsible for converting a given exception into an HTTP response that should be sent back to the browser. By default, the exception is passed to the base class which generates a response for you. You can check the exception type or return your own custom response like so:

```php
/**
 * Render an exception into an HTTP response.
 *
 * @param \Illuminate\Http\Request $request
 * @param \Exception $exception
 *
 * @return \Illuminate\Http\Response
 */
public function render($request, Exception $exception)
{
    if ($exception instanceof WordPressException) {
        return reponse()->view('errors.wordpress', [], 500);
    }
    
    return parent::render($request, $exception);
}
```

Reportable & renderable exceptions
----------------------------------

Instead of type-checking exceptions inside your `App\Exceptions\Handler` class's `report` and `render` methods, you may define `report` and `render` methods directly on your custom exception. When these methods exist, they will be called automatically by the framework:

```php
<?php

namespace App\Exceptions;

use Exception;

class WordPressException extends Exception
{
    /**
     * Report the exception.
     *
     * @param Exception $exception
     * 
     * @return void
     */
    public function report(Exception $exception)
    {
        //
    }
    
    /**
     * Render the exception into an HTTP response.
     *
     * @param Illuminate\Http\Request $request
     * @param Exception $exception
     *
     * @return Illuminate\Http\Response
     */
    public function render($request, Exception $exception)
    {
        return response(...);
    }
}
```

HTTP exceptions
---------------

Some exceptions describe HTTP error codes from the server. For example, this may be a "page not found" error (404), an "unauthorized error" (401) or even a developer genererated 500 error. In order to generate such a response from anywhere in your application, you may use the `abort` helper function like so:

```php
abort(404);
```

The `abort` helper will immediately raise an exception which will be rendered by the exception handler. Optionally, you may provide the response text:

```php
abort(403, 'You shall not pass.');
```

Custom HTTP error pages
-----------------------

The Themosis framework is using the same mechanism as with the Laravel framework in order to let developers customize error pages for the various HTTP status codes. For example, if you wish to customize the error page for 404 HTTP status code, create a `resources/views/errors/404.blade.php` file. This file will be served on all 404 errors generated by your application. The views within this directory should be named to match the HTTP status code they correspond to. The `HttpException` instance raised by the `abort` function will be passed to the view as an `$exception` variable:

```php
<p>{{ $exception->getMessage() }}</p>
```

You may also publish core framework error page templates using the console `vendor:publish` command. You can then customize the look and feel of each individual views:

```php
php console vendor:publish --tag=themosis-errors
```
