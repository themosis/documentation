Hooks
=====

- [Introduction](#introduction)
- [Default hooks](#default-hooks)
- [Basic hookable class](#basic-hookable-class)
- [Hookable action](#hookable-action)

Introduction
------------

Since release 2.0, it is now possible to extend WordPress from the application root. Instead of relying on plugins in order to add functionality to your WordPress application, you can leverage "hookable" classes to do so.

When you write code inside a "hookable" class, you have access to all APIs defined by WordPress and your application packages. By default, "hookable" classes are loaded just before mu-plugins. It is also possible to run the class code at a specific WordPress action hook and define its priority.

Default hooks
-------------

The Themosis framework is bundled with basic hookable classes. Those classes customize the default behavior of WordPress for you. The classes are stored inside the `app/Hooks` directory and you can customize or extend them for your needs.

Feel free to explore each one of them. Each class contains self-explanatory comments to guide you. Just note that the `Application` hook class is the primary one and is responsible of loading some core features and the core Themosis framework assets.

Hookable classes are not automatically registered in the application lifecycle. Each time you create a new hookable class, you must register it inside the `config/app.php` file in its `hooks` property. If you explore that file, you'll see default classes already registered.

Basic hookable class
--------------------

In order to create a new hookable class, you may call the `make:hook` command from the console:

```php
php console make:hook MyHook
```

The command will generate a new class and store it inside the `app/Hooks` folder of your application:

```php
<?php

namespace App\Hooks;

use Themosis\Hook\Hookable;

class MyHook extends Hookable
{
    /**
     * Extend WordPress.
     */
    public function register()
    {
        // Execute your code...
    }
}
```

Write your code inside the `register` method. Once your code is added, you still need to register the hookable class with the `hooks` property of the `config/app.php` file.

```php
'hooks' => [
    App\Hooks\MyHook::class
]
```

Hookable action
---------------

It is also possible to "post-pone" the execution of a hookable class to a specific WordPress action hook. For example, you may want to run the code inside the `register` method only when the `init` action is triggered by WordPress.

To do so, simply define a `$hook` property on your class and set its value to one of the WordPress action hook:

```php
<?php

namespace App\Hooks;

use Themosis\Hook\Hookable;

class HookOnInit extends Hookable
{
    public $hook = 'init';

    /**
     * Extend WordPress.
     */
    public function register()
    {
        // Code is executed on the "init" WordPress action only.
    }
}
```

It is also possible to define an execution priority by setting the `$priority` property like so:

```php
<?php

namespace App\Hooks;

use Themosis\Hook\Hookable;

class HookOnInit extends Hookable
{
    public $hook = 'init';

    public $priority = 20;

    /**
     * Extend WordPress.
     */
    public function register()
    {
        // Code is executed on the "init" WordPress action only.
    }
}
```

Hookable classes are only used at application root. There are used as an entry point into the WordPress APIs. If you develop custom plugins or work inside the theme, you don't need hookable classes because at that point, all WordPress APIs are loaded already.

If you need to execute some code at a specific action, simply use the [Action class]({{url}}/action) or the [Filter class]({{url}}/filter).

Next
----

Read the [views guide]({{url}}/views)



