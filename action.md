Action
======

- Basic usage
	- Priority and accepted arguments
	- Function and closure
	- Class

Basic usage
-----------

The `Action` class is a wrapper of the WordPress action/hook API. The class only handles a method in order to register/add action hooks.

To register an action, simply use the `add()` method like so:

```php
Action::add('init', 'ClassName@initiation');
```

The method allows you to register your action function/method using the same syntax as route's controller. Specify your class name followed by the `@` symbol and the method.

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

You can also omit the method name. By default, the API is looking for a class method with the same name as the action. So if your action is `init`, the API will use the `init` method of your class like so:

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

This class is very useful if you use classes to handle your code.

> Note: make sure to always set your method as `public` otherwise WordPress can't use it.

### Priority and accepted arguments

You can specify the priority and the number of arguments your action method/function will receive using the third and fourth parameter respectively. By default, the `priority` is set to `10` and the number of arguments to `3`.

Here is an example on how to set a priority and a number of accepted arguments:

```php
Action::add('init', 'AwesomeClass', 12, 2);
```

### Function and closure

The `Action::add()` method also works like the default WordPress function [add_action()](https://codex.wordpress.org/Function_Reference/add_action).

You can pass it a function name or a closure like so:

```php
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

### Class

The `add()` method also supports this default syntax in order to use a class:

```php
// Use it inside a class
Action::add('init', [$this, 'init']);

// With an instance.
$class = new ClassName();
Action::add('init', [$class, 'init']);
```