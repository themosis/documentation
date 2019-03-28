Widgets
=======

- [Introduction](#introduction)
- [Create a widget](#create-a-widget)
    - [Register the widget](#register-the-widget)

Introduction
------------

The Themosis framework provides the `App\Hooks\Widgets` Hookable class in order to register custom widgets for your application as well as a console command to scaffold a basic widget class.

Create a widget
---------------

You can create as many widgets as you want. Use the console `make:widget` command to create a new widget class:

```bash
php console make:widget LatestBooksWidget
```

The command will create a `LatestBooksWidget` class and store it inside the `App\Widgets` directory of your application.

```php
<?php

namespace App\Widgets;

class LatestBooksWidget extends \WP_Widget
{
    public function __construct()
    {
        parent::__construct('latest-books', 'Latest Books', [
            'description' => 'Display a list of the most recent published books.'
        ]);
    }
}

```

### Register the widget

Now that your widget is created, you need to register it. Open the `App\Hooks\Widgets.php`  file. The file is already registered inside the `config/app.php` file. See the hooks guide for more information on Hookable classes.

In order to register your widget, simply add it to the `$widgets` property of the `Widgets` hook class:

```php
<?php

namespace App\Hooks;

use Themosis\Hook\Hookable;

class Widgets extends Hookable
{
    ...
    
    /**
     * Widgets to register.
     *
     * @var array
     */
    public $widgets = [
        \App\Widgets\LatestBooksWidgets::class
    ];

    ...
}
```

The `register` method defined inside the class will take care of registering all defined widgets. You can now use namespaces with your widgets classes.