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
	public static function all(){

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
In order to use your model class, you must add it to the `models.config.php` file located in the `app/config` directory of the `themosis-plugin` plugin. The config file is used to auto-load the model classes using a class mapping. Add your model like so:

```php

// Key is the class name and the value is the path to the class file.
'PostModel'		=> themosis_path('plugin').'models'.DS.'PostModel.php'
```

The function `themosis_path('plugin')` return the plugin `app` folder path. More information about the `themosis_path` function in the [Helpers guide]().

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

Discover the other features/API of the framework below:

* [Ajax](https://github.com/themosis/documentation/blob/master/ajax.md)
* [Asset](https://github.com/themosis/documentation/blob/master/asset.md)
* [PostType](https://github.com/themosis/documentation/blob/master/posttype.md)
* [Field](https://github.com/themosis/documentation/blob/master/field.md)
* [Metabox](https://github.com/themosis/documentation/blob/master/metabox.md)
* [Taxonomy](https://github.com/themosis/documentation/blob/master/taxonomy.md)
* [Page](https://github.com/themosis/documentation/blob/master/page.md)
* [Option](https://github.com/themosis/documentation/blob/master/option.md)
* [Helpers](https://github.com/themosis/documentation/blob/master/helpers.md)