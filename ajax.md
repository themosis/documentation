Ajax
====

1. Introduction
2. Themosis Global Object

1. Introduction
---------------

The `Ajax` class handles the WordPress mechanism for easily handling ajax requests.

**Note**: All the code in order to customize WordPress should be stored inside the `app/admin` directory.

In order to listen to a WordPress ajax action, use the method below:

```php
Ajax::run('my_action', 'both', function(){
	
	// Perform security check before anything - nonce

	$value = $_POST['key'];

	// Perform your WordPress actions...

});
```

Here are the method details:
```php
Ajax::run($action, $logged, $closure);
```

* `$action`: _string_. Your custom action name for the Ajax request.
* `$logged`: _string_. Possible values are `yes`, `no`, `both`. You tell if the ajax action should be performed for logged in or logged out users or both.
* `$closure`: _callback_. A callback function where you run your custom code.

### Example:
The code below shows a simple AJAX process. It uses a javascript file and a php file. Check the comments in the code for more tips/hints.

```js
// This file is stored in app/assets/js/application.js
// Let's assume this ajax request is made when a user click a button.
// When the ajax request is done, we simply display it in the console.

$.ajax({

    url: themosis.ajaxurl, // Global access to the WordPress ajax handler file
    type: 'POST',
    dataType: 'json',
    data: {

        action: 'my-custom-action', // Your custom hook/action name
        security: themosis.security, // A nonce value
        number: 2 // The value you want to send

    }

}).done(function(data){
	
	// This should print "4" in the console.
	console.log(data);

});
```

Now that the user has clicked the button and ran the ajax request. Let's handle it with our `Ajax` class.

```php
<?php
	// This file is stored in app/admin/ajax.php
	// This code listens for logged in and logged out users
	Ajax::run('my-custom-action', 'both', function(){
		
		// Check nonce value
		check_ajax_referer(Session::nonceName, 'security');

		// Run custom code - Make sure to sanitize and check values before
		$result = 2 + $_POST['number'];
		
		// "Return" the result
		echo $result;

		// Close
		die();

	});
?>
```
> The code above is using the `Session::nonceName` for the `check_ajax_referer` action name parameter. The session class only provides 2 constants you can use for WordPress nonce functions:
> 
> - **Session::nonceName**
> - **Session::nonceAction**
> 
> WordPress does not use PHP session so there is no real session API provided by the framework yet.

2. Themosis Global Object
-------------------------

In the previous javascript example, we get access to some values using the following syntax:

```js
{
	url: themosis.ajaxurl
}
```

This `themosis` global json object is located at the end of the closing `</head>` tag of any pages. By default, it contains the key/value pair `ajaxurl` but you can easily add more key/value pairs to this global object and access them in your code. 

To add more values, you need to use a framework filter call `themosisGlobalObject`. Here's an example:

```php
add_filter('themosisGlobalObject', function($datas){

	$datas['myData'] = 'Some value';

	return $datas;

});
```

This will output the following object:

```js
var themosis = {
	
	ajaxurl = 'http://www.my-domain.com/wp-admin/admin-ajax.php',
	myData = 'Some value'

}
```

### Change the Themosis global object name

You can easily change the variable name of this global object. In order to do so, open the `app/config/application.config.php` file and change the `namespace` value:

```php
array(

	'namespace' => 'themosis' // Change this value...

)
```

***

### More resources:

Here are a few other resources about WordPress Ajax:

* [WordPress codex](http://codex.wordpress.org/AJAX)
* [check\_ajax\_referer function](https://codex.wordpress.org/Function_Reference/check_ajax_referer)
* [wp\_localize\_script function](http://codex.wordpress.org/Function_Reference/wp_localize_script)