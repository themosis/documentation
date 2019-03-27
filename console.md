Console
=======

- [Introduction](#introduction)
- [Writing commands](#writing-commands)
    - [Generate a command](#generate-a-command)
    - [Command structure](#command-structure)
    - [Closure commands](#closure-commands)
- [Learn more](#learn-more)

Introduction
------------

The Themosis framework provides a CLI tool with utility commands as well as an interface in order to help you define commands for your application.

The `console` CLI tool is independent of a WordPress instance. Therefore you can run commands before WordPress is even installed.

Writing commands
----------------

In addition the the commands provided by the console, you can also build your own custom commands. Commands are typically stored in the `app/Console/Commands` directory. However you are free to choose your own storage location as long as your commands can be loaded by Composer.

### Generate a command

In order to create a new command, use the `make:command` console command. The command creates a new command class in the `app/Console/Commands` directory. If the directory does not yet exist, it will be created the first time you run the `make:command` console command:

```bash
php console make:command DumpDatabase
```

### Command structure

After generating your command, you should fill in the `$signature` and `$description` properties of the class, which will be used when displaying your command on the list screen.

Then `handle` method will be called when your command is executed. You may place your command logic in this method.

> It is recommended to keep your commands light and let them defer to application services to accomplish their tasks.

Let's take a look at an example command. Note that we are able to inject any dependencies we need into the command's `handle` method. The service container will automatically inject all dependencies that are type-hinted in the method's signature:

```php
<?php

namespace App\Console\Commands;

use App\Services\MySqlDumper;
use Illuminate\Console\Command;

class DumpDatabase extends Command
{
    /**
     * The console command name.
     *
     * @var string
     */
    protected $signature = 'dump:database {name}';

    /**
     * The console command description.
     *
     * @var string
     */
    protected $description = 'Dump a given MySQL database';

    /**
     * Create a new command instance.
     *
     * @return void
     */
    public function __construct()
    {
        parent::__construct();
    }

    /**
     * Execute the console command.
     *
     * @return mixed
     */
    public function handle(MySqlDumper $dumper)
    {
        $dumper->dump($this->argument('name'), storage_path('backup/database'))
    }
}
```

### Closure commands

Closure based commands provide an alternative to defining console commands as classes. Within the `commands` method of your `app/Console/Kernel.php` file, the Themosis framework loads the `routes/console.php` file:

```php
/**
 * Register the commands for the application.
 */
protected function commands()
{
    require base_path('routes/console.php');
}
```

Even though this file does not define HTTP routes, it defines console based entry points into your application. Within this file, you may define all of your Closure based commands using the `Console::command` method. The `command` method accepts two arguments: the command signature and a Closure which receives the commands arguments and options:

```php
Console::command('hello {name}', function ($name) {
    $this->info("Hello {$name}!");
});
```

The Closure is bound to the underlying command instance, so you have full access to all of the helper methods you would typically be able to access on a full command class.

#### Type-hinting dependencies

In addition to receiving your command's arguments and options, command Closures may also type-hint additional dependencies that you would like resolved out of the service container:

```php
Console::command('dump:database {name}', function (MySqlDumper $dumper, $name) {
    $dumper->dump($name, storage_path('backup/database'));
});
```

#### Closure command descriptions

When defining Closure based command, you may use the `describe` method to add a description to the command. This description will be displayed when you run the `php console list` or `php console help` commands:

```php
Console::command('hello {name}', function ($name) {
    $this.>info("Hello {$name}!");
})->describe('Say hello to someone');
```

Learn more
----------

The Themosis framework `console` is based on the `illuminate/console` package from the Laravel framework. Read the official documentation for more details about the console and its API:

- [Defining input expectations](https://laravel.com/docs/artisan#defining-input-expectations)
- [Command I/O](https://laravel.com/docs/artisan#command-io)
- [Registering commands](https://laravel.com/docs/artisan#registering-commands)
- [Programmatically executing commands](https://laravel.com/docs/artisan#programmatically-executing-commands)