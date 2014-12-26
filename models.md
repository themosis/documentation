Models
======

1. Introduction
2. Register a Model

1. Introduction
---------------

Themosis framework comes with a basic system of models.

A model will store the methods/functions that manipulate your data. You can perform actions to retrieve data from your WordPress database using WordPress core functions or you can perform actions to update those data.

2. Register a Model
-------------------

Here is an example of a model class that comes by default with the framework:

```php
<?php

// This file is stored in 'app/models/PostModel.php'

class PostModel
{
	/**
	 * Return a list of all published posts.
	 * 
	 * @return array
	*/
	public static function all()
	{
		$query = new WP_Query(array(
            'post_type'         => 'post',
            'posts_per_page'    => -1,
            'post_status'       => 'publish'
        ));

        return $query->get_posts();
	}
}

?>
```
In order to use your model class, you must add it to the `loading.config.php` file located in the `app/config` directory of the `themosis-theme` theme. The config file is used to auto-load the model classes using a class mapping. Add your model like so:

```php
// Key is the class name and the value is the path to the class file.
'PostModel'		=> themosis_path('app').'models'.DS.'PostModel.php'
```

The function `themosis_path('app')` returns the `app` folder path. More information about the `themosis_path` function in the [Helpers guide](http://framework.themosis.com/docs/helpers/).

Then we can retrieve all the `posts` for our view like so:

```php
Route::get('home', function(){

	return View::make('pages.home', array(

		'posts' => PostModel::all()
	
	));

});
```

> Store your `model` classes in the `app/models` directory.

Next
----
This article closes the "Getting started" guide.

There are APIs that will help you shape your WordPress administration. Discover those features/API of the framework below:

* [Ajax](http://framework.themosis.com/docs/ajax/)
* [Asset](http://framework.themosis.com/docs/asset/)
* [Configuration](http://framework.themosis.com/docs/configuration/)
* [PostType](http://framework.themosis.com/docs/posttype/)
* [Form](http://framework.themosis.com/docs/form/)
* [Field](http://framework.themosis.com/docs/field/)
* [Meta](http://framework.themosis.com/docs/meta/)
* [Metabox](http://framework.themosis.com/docs/metabox/)
* [Taxonomy](http://framework.themosis.com/docs/taxonomy/)
* [Page](http://framework.themosis.com/docs/page/)
* [Option](http://framework.themosis.com/docs/option/)
* [User](http://framework.themosis.com/docs/user/)
* [Validation and input](http://framework.themosis.com/docs/validation/)
* [Widgets](http://framework.themosis.com/docs/widgets/)
* [Helpers](http://framework.themosis.com/docs/helpers/)