Service providers
=================

**Work in progress** guide.

- [Introduction](#introduction)
- Write a service provider
- Register the service provider

Introduction
------------

Since release 1.3, we can register Service Providers in order to customize our application.

Basically, services providers allow you to register objects into the service container, bootstrap your code or any libraries. For example, the full stack framework comes with a pre-defined `RoutingService` class that registers the theme and plugin routes.

One general rule to keep in mind is that service providers are always read before any other files located inside the `resources` directory. Meaning that if you need to define some dependencies or setup data before working on the WordPress administration or on the front-end side, service providers are the way to go.

Write a service provider
------------------------

Service providers extend the `Themosis\Foundation\ServiceProvider` class which extends the `Illuminate\Support\ServiceProvider` from Laravel. Service providers contain a `register()` and `boot()` methods.

Here is an example of a basic service provider for the **theme**. For this example, let's register the `CommonMark` class from the [PHP League](http://commonmark.thephpleague.com/basic-usage/):

```php
<?php

namespace Theme\Providers;

use Themosis\Foundation\ServiceProvider;

class DemoService extends ServiceProvider
{
    /**
     * Let's register any bindings through the help of the
     * register method.
     */
    public function register()
    {
        $this->app->bind('');
    }
}
```






