Models
======

1. Introduction
2. Register a Model

1. Introduction
---------------

Themosis framework comes with a basic system of models.

A model will store the methods/functions that manipulate your datas. You can perform actions to retrieve datas from your WordPress database using WordPress core functions or you can perform actions to update those datas.

At the moment, Themosis framework allows you to build your model as you want but in order to avoid class name conflicts at run-time, you have to follow a default convention for your files's name and class name.

2. Register a Model
-------------------

Here is an example of a model class:

```php
<?php

// This file is stored in 'app/models/books.model.php'

class Books_Model extends BaseModel{

	/**
	 * Retrieve all books custom post type
	 * with a slug of 'books'.
	 * 
	 * @return array
	*/
	public static function all(){

		$query = new WP_Query(array(
		
			'post_type'			=> 'books',
			'posts_per_page'	=> -1

		));

		return $query->get_posts();

	}

}

?>
```

Then we can retrieve all the books for our view like so:

```php
Route::is('home', function(){

	return View::make('pages.home', array(

		'books' => Books::all() // The name of the class does not use
	
	));

});
```

All your `models` classes should be stored in the `app/models` directory and use the `.model.php` file extension.

By convention, the name of your file before the extension `.model.php` must be lowercase and be used as a class name with its first letter to uppercase followed by `_Model`. This name is also the model's class name used when you call it.

And all your models should extend the `BaseModel` class.

Here is another example to illustrate the name convention for a file stored in `app/models/news.model.php`:

```php
// The file is stored in 'app/models/news.model.php'

class News_Model extends BaseModel{

	// Some methods...

}
```

You can call the model by using its name: `News::someMethod()`

### Resolve name conflicts

If there is a name conflict at run-time, you can define a class property `$alias` to change the class alias name.

Here is an example using the previous `news.model.php` class.

```php
class News_model extends BaseModel{

	private $alias = 'Articles';

	// Some methods

}
```
You can now call your class using this `$alias` name:

```php
Route::is('home', function(){

	return View::make('home', array(

		'news'	=> Articles::someMethod()

	));

});
```
The `Articles::someMethod` class is calling behind the `News::someMethod` class.


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