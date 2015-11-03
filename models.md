Models
======

- Introduction
- Register a Model
- Basic usage

Introduction
------------

Themosis framework comes with a basic system of models.

A model will store the methods/functions that manipulate your data. You can perform actions to retrieve data from your WordPress database using WordPress core functions or you can perform actions to update those data.

Register a Model
----------------

In order to create a model, simply create a new PHP class inside the `resources/models` directory. Here is an example of a model class that comes by default with the framework:

```php
<?php

// This file is stored in 'resources/models/PostModel.php'

class PostModel
{
	/**
	 * Return a list of all published posts.
	 * 
	 * @return array
	*/
	public static function all()
	{
		$query = new WP_Query([
            'post_type'         => 'post',
            'posts_per_page'    => -1,
            'post_status'       => 'publish'
        ]);

        return $query->get_posts();
	}
}

?>
```
By default, model classes are loaded using the PSR-4 standard and are setup without any namespaces.

If you want to add directories to autoload custom classes or add namespaces to your existing models, use the `loading.config.php` file stored inside the `resources/config` directory of your `themosis-theme`:

```php
<?php

return [

    /*
    * Edit this file in order to configure your theme's
    * classes autoloading. Classes are loaded using PSR-4.
    *
    * The key is the namespace and key's value contains one or more paths to your classes.
    */
    ''  => [themosis_path('theme').'controllers', themosis_path('theme').'models']

];
```

The function `themosis_path('theme')` returns the theme `resources` folder path. More information about the `themosis_path` function in the [Helpers guide](http://framework.themosis.com/docs/helpers/).

Basic usage
-----------

In order to use your model, simply create a new instance anywhere in your code or if you used static methods, only call your method. Using the above example code, we can retrieve all the `posts` for our view like so:

```php
Route::get('home', function()
{
	return View::make('pages.home', [
		'posts' => PostModel::all()
	]);
});
```

> Store your `model` classes in the `resources/models` directory.

Next
----
Read the [views guide](http://framework.themosis.com/docs/views/)