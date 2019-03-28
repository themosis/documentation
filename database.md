Database
========

- [Introduction](#introduction)
- [Configuration](#configuration)
- [Migrations](#migrations)

Introduction
------------

Since release 1.3, the Themosis framework is bundled with the `Illuminate/database` package. It provides a performant [database query builder](https://laravel.com/docs/queries) as well as the popular [Eloquent ORM](https://laravel.com/docs/eloquent).

The database guide won't provide a full copy of the official documentation but instead give you notes about possible differences on how things are managed or organized within the Themosis framework.

For detailed information about the database package API, please read the official documentation:
- [Laravel database](https://laravel.com/docs/5.8/database)
- [Laravel Eloquent ORM](https://laravel.com/docs/eloquent)

Configuration
-------------

The Themosis framework is a WordPress application stack. It is configured to handle a **MySQL** database connection by **default**. Database configuration is done inside your application `config/database.php` configuration file.

You can also define multiple database connections to let your application read/write data from another data source but those connections are not tight with your WordPress instance.

Migrations
----------

Migrations are like version control for your database, allowing anyone on your team to easily modify and share the application's database schema. The Themosis framework is now handling the Laravel schema builder in order to easily build your application's database schema.

The `Schema` facade provides database agnostic support for creating and manipulating tables accross all supported database systems.

### Generate migrations

In order to create a new migration, use the `make:migration` console command:

```bash
php console make:migration create_customers_table
```

The migration file is placed into the `database/migrations` directory. See the [official documentation](https://laravel.com/docs/migrations#migration-structure) for detailed explanation on how to write migrations.


### Running migrations

To run all migrations, simply call the `migrate` console command:

```bash
php console migrate
```

> If you use the Homestead virtual machine, make sure to run this command from the virtual machine.

Migration operations may be destructive. In order to protect you from running these commands against your production database, you will be prompted for confirmation before the commands are executed. To force a migration command to run, use the `--force` flag:

```bash
php console migrate --force
```

### Rolling back migrations

All rolling back commands are available though the console but you **must** take extra care when executing the `migrate:fresh` command. That command is going to drop all your database tables. As WordPress tables are not yet handled by migrations, executing that command will make you lose all your data.

```bash
php console migrate:install
php console migrate:refresh
php console migrate:reset
php console migrate:rollback
php console migrate:status
```