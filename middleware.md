Middleware
==========

- [Introduction](#introduction)
- [Defining middleware](#defining-middleware)
- [Registering middleware](#registering-middleware)

Introduction
------------

The Themosis framework is now fully compatible with the middleware mechanism provided by the `illuminate/routing` package.

Middleware provide a convenient mechanism for filtering HTTP requests entering your application. For example, the Themosis framework includes a middleware that verifies if the current user has the correct capability to visit a URL. If the current user has not the specified capability defined by the middleware, the user is redirected to the home page. Else if the user has the capability, the middleware allow the request to proceed further into the application.

> Middleware provided by Illuminate packages are not all activated/defined in the HTTP kernel by default. Only the `SubstituteBindings` middleware class is active.

Defining middleware
-------------------

In order to create a new middleware, use the `make:middleware` console command:

```bash
php console make:middleware CheckUserRole
```

This command will create a new `CheckUserRole` class within the `app/Http/Middleware` directory:

```php
<?php

namespace App\Http\Middleware;

use Closure;

class CheckUserRole
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        if ($request->role !== 'editor') {
            return redirect('home');
        }

        return $next($request);
    }
}
```

In the example above, if the given `role` is not an `editor`, the middleware returns a HTTP redirect to the client. Otherwise, the request is passed further into the application. In order to pass the request deeper into the application, call the `$next` callback with the `$request`.

It's best to envision middleware as a series of "layers" HTTP requests must pass through before they hit your application. Each layer can examine the request and even reject it entirely.

> All middleware are resolved via the service container, so you may type-hint any dependencies you need within a middleware's constructor.

Registering middleware
----------------------

### Global middleware

If you want a middleware to run during every HTTP request to your application, list the middleware class in the `$middleware` property of your `app/Http/Kernel.php` class.

### Assigning middleware to routes




