Models
======

- [Introduction](#introduction)
- [Basic models](#basic-models)
- [WordPress database](#wordpress-database)

Introduction
------------

The Themosis framework comes with full support of the `illuminate/database` package and provides [Laravel Eloquent ORM](https://laravel.com/docs/eloquent) to your application.

Eloquent is an ActiveRecord implementation for working with your database. Each database table has a corresponding "Model" which is used to interact with that table. Models allow you to query for data in your tables, as well as insert new records into the table.

By default, your application is configured to connect to a `MySQL` database. You can manage your database configuration in the `config/database.php` file.

The Themosis framework is bundled with the following database tools:

- [Database query builder](https://laravel.com/docs/database)
- [Eloquent ORM](https://laravel.com/docs/eloquent)
- [Database migration](https://laravel.com/docs/migrations)
- [Collections](https://laravel.com/docs/eloquent-collections)

For more details on what is available by these tools and their full configuration, please [refer to the official documentation](https://laravel.com/docs/database#configuration).

Basic models
------------

In order to create a model, simply create a new PHP class inside the root `app/Models` directory of your application by using the `make:model` command:

```bash
php artisan make:model Book
```

The above command will generate a model class inside your `app/Models` folder, but you are free to place them anywhere that can be autoloaded according to your `composer.json` file. All Eloquent models extends `Illuminate\Database\Eloquent\Model` class:

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Book extends Model
{
    //
}
```

If you would like to generate a database migration when you generate the model, you may use the `--migration` or `-m` option:

```bash
php artisan make:model Book --migration

php artisan make:model Book -m
```

For more details on how to configure your models, retrieving models, inserting, updating, deleting models and more, please follow [the official documentation](https://laravel.com/docs/eloquent).

WordPress database
------------------

The Themosis framework does not provide models by default to manage WordPress database tables data. But here is an example on how to configure a model to manage the `wp_posts` database table.

First, let's create a `Post` model class using the `make:model` command:

```bash
php artisan make:model Post
```

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    //
}
```

By convention, Eloquent use a "snake case", plural name of the class as the table name. So by default, our `Post` model expects a database table with a name of `posts`, which is the name of the table defined by WordPress without its prefix (`wp_posts` - depending on your database prefix configuration).

> **Table name prefix is automatically handled**. You can define it in [your environment configuration]({{url}}/configuration). See the `config/database.php` configuration file for the available variables.

In the case where you need to customize the table name for your model, use the `$table` instance property like so:

```php
// The model is looking after a table
// with the "wp_custom_table" name.
protected $table = 'custom_table';
```

> **Do not specify the table prefix** even for a custom name as this is handled automatically.

By default, Eloquent models expect that the primary key is an `id` column. However, WordPress is setting the `wp_posts` table primary key column to `ID`. Let's update the default model and set the `$primaryKey` property of the model:

```php
/**
 * @var string
 */
protected $primaryKey = 'ID';
```

Each Eloquent models also expect that your database table handle timestamps using a `created_at` and `updated_at` columns. WordPress, in our case is using the `post_date` and `post_modified` columns. Let's update the model to handle those columns:

```php
const CREATED_AT = 'post_date';
const UPDATED_AT = 'post_modified';
```

Here is the code sample for a custom `Post` model class that allow you to manage your WordPress posts using Eloquent:

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    const CREATED_AT = 'post_date';
    const UPDATED_AT = 'post_modified';

    /**
     * @var string
     */
    protected $primaryKey = 'ID';
}
```

Read the [official documentation](https://laravel.com/docs/eloquent) for more details about Eloquent ORM.

Next
----
Read the [hooks guide]({{url}}/hooks)