Models
======

- [Introduction](#introduction)
- [Register a Model](#register-a-model)
    - [Add Eloquent ORM support](#add-eloquent-orm-support)
- [Basic usage](#basic-usage)

Introduction
------------

Themosis framework comes with a basic system of models and if you're using the full stack of the Themosis framework, you can extend your model class to use the popular Laravel Eloquent ORM.

A model will store the methods/functions that manipulate your data. You can perform actions to read, update and delete data from your WordPress database, using both WordPress core functions and/or Illuminate/Database API.

> The Query Builder and Eloquent ORM API from the Illuminate/database package are only available if you're using the full stack Themosis framework. Meaning that your project is based on the `themosis/themosis` package and not a classic WordPress installation with the framework loaded.

Register a Model
----------------

In order to create a model, simply create a new PHP class inside the `resources/models` directory of your theme or custom plugin. Here is an example of a simple model class:

```php
<?php

namespace Theme\Models;

class Post
{
    /**
     * Return a list of all published posts.
     * (not recommended)
     * 
     * @return array
     */
    public function all()
    {
        $query = new WP_Query([
            'post_type' => 'post',
            'posts_per_page' => -1,
            'post_status' => 'publish'
        ]);

        return $query->get_posts();
    }
}
```
By default, model classes are loaded using the PSR-4 standard. Models from the theme use the `Theme\Models` namespace.

If you want to change your theme models namespace, use the `loading.config.php` file stored inside the `resources/config` directory of your `themosis-theme`:

```php
<?php

return [

    /*
    * Edit this file in order to configure your theme's
    * classes autoloading. Classes are loaded using PSR-4.
    *
    * The key is the namespace and key's value contains one or more paths to your classes.
    */
    'Theme\\Controllers\\' => themosis_path('theme.resources').'controllers',
    'Theme\\Models\\' => themosis_path('theme.resources').'models',
    'Theme\\Providers\\' => themosis_path('theme.resources').'providers'

];
```

The function `themosis_path('theme.resources')` returns the theme `resources` folder path. More information about the `themosis_path` function in the [Helpers guide]({{url}}/helpers).

### Add Eloquent ORM support

Developers familiar with the Illuminate/database package can set their models to extend the Eloquent ORM. Here is an example of a model class extending the above simple post model:

```php
<?php

namespace Theme\Models;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    /**
     * The post model class fetch its records
     * from the "wp_posts" table.
     * The DB prefix is already defined for you.
     */
    protected $table = 'posts';

    /**
     * Eloquent needs to know which column in
     * the wp_posts table is the primary key.
     */
    protected $primaryKey = 'ID';

    /**
     * WordPress do not have "created_at" and "updated_at"
     * columns. Or define class const CREATED_AT and UPDATED_AT
     */
    public $timestamps = false;
}
```

You can also define custom timestamps columns using the const `CREATED_AT` and `UPDATED_AT` and specify the `post_date` and `post_modified` columns in place of not handling timestamps at all.

> For more information about Eloquent ORM configuration, please read the [Laravel documentation.](https://laravel.com/docs/5.3/eloquent)

Basic usage
-----------

In order to use your model, simply create a new instance. Using the above example code, we can retrieve all the `posts` for our view like so:

```php
// Using the simple model representation.
// Route callback can auto instantiate classes as well.
Route::get('home', function (Post $model) {
    return view('pages.home', [
        'posts' => $model->all()
    ]);
});
```

In the previous example, it returns an array of WP_Post objects. Now you can use the same approach with our model that extends the Eloquent ORM like so:

```php
Route::get('home', function (Post $model) {
    return view('pages.all', [
        'posts' => $model->all() // Now the all() method is coming from the Eloquent base model
    ]);
});
```

> Store your `model` classes in the `resources/models` directory of your theme or custom plugin.

Next
----
Read the [views guide]({{url}}/views)