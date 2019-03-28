Action
======

- [Register an action](#register-an-action)
    - [Priority and accepted arguments](#priority-and-accepted-arguments)
    - [Function, closure and class](#function-closure-and-class)
- [Constructor injection](#constructor-injection)
- [Run an action](#run-an-action)
- [Check action existence](#check-action-existence)
- [Remove an action](#remove-an-action)
- [Retrieve action callback](#retrieve-action-callback)

Register an action
------------------

The `Action` class is a wrapper of the WordPress action/hook API. To register an action, simply use the `add()` method like so:

```php
Action::add('init', 'ClassName@initiation');
```

The method allows you to register your action function/method using the same syntax as a route's controller. Specify your full class name followed by the `@` symbol and the method.

The code above is calling the `initiation` method of the `ClassName` class:

```php
class ClassName
{
    public function initiation()
    {
        // Call by the init action.
        // Code..
    }
}
```

> Note that you **must** provide the full class name with its namespace.

You can also omit the method name. By default, the API is looking for a class method with the same name as the action. So if your action is `init`, the class will look after the `init` method:

```php
Action::add('init', 'ClassName');

// ClassName
class ClassName
{
    public function init()
    {
        // Code...
    }
}
```

> Note: make sure to always set your method as `public` otherwise WordPress can't use it.

### Priority and accepted arguments

You can specify the priority and the number of arguments your action method/function will receive using the third and fourth arguments respectively. By default, the `priority` is set to `10` and the number of arguments to `3`.

Here is an example on how to set a priority and a number of accepted arguments:

```php
Action::add('init', 'AwesomeClass', 5, 2);
```

### Function, closure and class

The `Action::add()` method also works like the default WordPress function [add_action()](https://developer.wordpress.org/reference/functions/add_action/).

You can pass it a function name or a closure like so:

```php
// Using a function
function callback()
{
    // Code...
}

Action::add('init', 'callback');

// Using a closure
Action::add('init', function()
{
    // Code...
});
```

The `add()` method also supports this default syntax in order to use a class:

```php
// Use it inside a class
Action::add('init', [$this, 'init']);

// With an instance.
$class = new ClassName();
Action::add('init', [$class, 'init']);
```

Constructor injection
---------------------

If you use the `Namespace\Classname@method` syntax for your action, you can now type-hint your class dependencies in the constructor method as it is automatically resolved by the application service container:

```php
<?php

namespace App\Hooks\Modules;

use App\Services\Shop\Metrics;
use Illuminate\Http\Request;

class Shop
{
    protected $request;
    
    protected $metrics;
    
    public function __construct(Request $request, Metrics $metrics)
    {
        $this->request = $request;
        $this->metrics = $metrics;
    }
    
    /**
     * Method automatically called by the "init" action hook.
     */
    public function init()
    {
        // Code...
    }
}
```

If you call the `init` action and attach the above class example, your will automatically get the `Illuminate\Http\Request` and `App\Services\Shop\Metrics` class instances injected:

```php
Action::add('init', 'App\Hooks\Modules\Shop');
```

Run an action
-------------

In order to trigger an action, use the `run()` method. The `run()` method is equivalent to WordPress core function `do_action` and `do_action_ref_array()`. Here is an example:

```php
use Themosis\Facades\Action;

Action::run('custom', $args);
```

The first argument is the name of your action hook. The second parameter is optional and let you pass data that can be accessed into registered callback.

Check action existence
----------------------

You can verify the existence of an action by using the `exists()` method. Current implementation check the existence of any actions registered through the Action class.

```php
use Themosis\Facades\Action;

if (Action::exists('custom')) {
    // Run some code...
}
```

Remove an action
----------------

In order to remove an action, use the `remove()` method like so:

```php
// Using default priority.
$action = Action::add('init', 'SomeClass@method');
$action->remove('init');

// Using custom priority - The priority must be defined as well on remove if different than 10.
$action = Action::add('init', 'SomeClass@method', 5);
$action->remove('init', 'SomeClass@method', 5);
```

Retrieve action callback
------------------------

The action class also provides a public method to help you retrieve the associated callback for your registered action. Use the `getCallback()` method:

```php
$action = Action::add('init', 'SomeClass@method');
$cb = $action->getCallback('init');
```
