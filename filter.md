Filter
======

- [Register a filter](#register-a-filter)
    - [Priority and accepted arguments](#priority-and-accepted-arguments)
    - [Function, closure and class](#function-closure-and-class)
- [Constructor injection](#constructor-injection)
- [Run a filter](#run-a-filter)
- [Check filter existence](#check-filter-existence)
- [Remove a filter](#remove-a-filter)
- [Retrieve filter callback](#retrieve-filter-callback)

Register a filter
-----------------

The `Filter` class is a wrapper of the WordPress filter/hook API. To register a filter, simply use the `add()` method like so:

```php
Filter::add('wp_terms_checklist_args', 'ClassName@checklist');
```

The method allows you to register your filter function/method using the same syntax as a route's controller. Specify your full class name followed by the `@` symbol and the method.

The code above is calling the `checklist` method of the `ClassName` class:

```php
class ClassName
{
    public function checklist()
    {
        // Call by the wp_terms_checklist_args filter.
        // Code..
    }
}
```

> Note that you **must** provide the full class name with its namespace.

You can also omit the method name. By default, the API is looking for a class method with the same name as the filter. So if your filter is `wp_terms_checklist_args`, the class will look after the `wp_terms_checklist_args` method:

```php
Filter::add('wp_terms_checklist_args', 'ClassName');

// ClassName
class ClassName
{
    public function wp_terms_checklist_args()
    {
        // Code...
    }
}
```

> Note: make sure to always set your method as `public` otherwise WordPress can't use it.

### Priority and accepted arguments

You can specify the priority and the number of arguments your filter method/function will receive using the third and fourth parameters respectively. By default, the `priority` is set to `10` and the number of arguments to `3`.

Here is an example on how to set a priority and a number of accepted arguments:

```php
Filter::add('get_attached_file', 'AwesomeClass', 5, 2);
```

### Function, closure and class

The `Filter::add()` method also works like the default WordPress function [add_filter()](https://developer.wordpress.org/reference/functions/add_filter/).

You can pass it a function name or a closure like so:

```php
// Using a function
function callback()
{
    // Code...
}

Filter::add('update_attached_file', 'callback');

// Using a closure
Filter::add('register_post_type_args', function()
{
    // Code...
});
```

The `add()` method also supports this default syntax in order to use a class:

```php
// Use it inside a class
Filter::add('do_mu_upgrade', [$this, 'upgrade']);

// With an instance.
$class = new ClassName();
Filter::add('customize_panel_active', [$class, 'panel']);
```

Constructor injection
---------------------

If you use the `Namespace\Classname@method` syntax for your filter, you can now type-hint your class dependencies in the constructor method as it is automatically resolved by the application service container:

```php
<?php

namespace App\Hooks\Modules;

use App\Services\Editor\Tools;
use Illuminate\Http\Request;

class Shop
{
    protected $request;
    
    protected $tools;
    
    public function __construct(Request $request, Tools $tools)
    {
        $this->request = $request;
        $this->tools = $tools;
    }
    
    /**
     * Method automatically called by the "content" filter hook.
     */
    public function content()
    {
        // Code...
    }
}
```

If you call the `content` filter and attach the above class example, your will automatically get the `Illuminate\Http\Request` and `App\Services\Editor\Tools` class instances injected:

```php
Filter::add('content', 'App\Hooks\Modules\Editor');
```

Run a filter
------------

In order to trigger a filter, use the `run()` method. The `run()` method is equivalent to WordPress core function `apply_filters` and `apply_filters_ref_array()`. Here is an example:

```php
use Themosis\Support\Facades\Filter;

Filter::run('custom', $args);
```

The first argument is the name of your filter hook. The second parameter is optional and let you pass data that can be accessed into registered callback.

Check filter existence
----------------------

You can verify the existence of a filter by using the `exists()` method. Current implementation check the existence of any filters registered through the Filter class.

```php
use Themosis\Facades\Filter;

if (Filter::exists('custom')) {
    // Run some code...
}
```

Remove a filter
---------------

In order to remove a filter, use the `remove()` method like so:

```php
// Using default priority.
$filter = Filter::add('wp_editor_settings', 'SomeClass@method');
$filter->remove('wp_editor_settings');

// Using custom priority - The priority must be defined as well on remove if different than 10.
$filter = Filter::add('the_editor_content', 'SomeClass@method', 5);
$action->remove('the_editor_content', 5);
```

Retrieve filter callback
------------------------

The filter class also provides a public method to help you retrieve the associated callback for your registered filter. Use the `getCallback()` method:

```php
$filter = Filter::add('https_ssl_verify', 'SomeClass@method');
$cb = $filter->getCallback('init');
```
