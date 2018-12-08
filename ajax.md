Ajax
====

- [Basic usage](#basic-usage)
	- [Example](#example)
- [Themosis Global Object](#themosis-global-object)
	- [Customize object name](#customize-object-name)
	- [WordPress localize](#wordpress-localize)

Basic usage
-----------

The `Ajax` class handles the WordPress mechanism for easily handling ajax requests.

In order to listen to a WordPress ajax action, use the `listen` method:

```php
Ajax::listen('my_action', function() {	
    // Perform security check before anything - nonce
    $value = $_POST['key'];

    // Perform your WordPress actions...
});
```

Here are the method details:

```php
Ajax::listen($action, $callback, $logged);
```

* $action _string_: Your custom action name for the Ajax request.
* $callback _string_: A callback function where you run your custom code.
* $logged _callback_: Possible values are `true`, `false`, `both`. You tell if the ajax action should be performed for logged in(true) or logged out(false) users or both.

### Example:

The code below shows a simple AJAX example. It uses a JavaScript and a PHP file. Check the comments in the code for more information:

```js
// This file is stored in assets/js/application.js
// Let's assume this ajax request is made when a user click a button.
// When the ajax request is done, we simply display it in the console.
$.ajax({
    url: themosis.ajaxurl, // Global access to the WordPress ajax handler file
    type: 'POST',
    dataType: 'json',
    data: {
        action: 'my-custom-action', // Your custom hook/action name
        security: 'add-posts', // A nonce value
        number: 2 // The value you want to send
    }
}).done(function(data)
{	
    // This should print "4" in the console.
    console.log(data);
});
```

Now that the user has clicked the button and ran the ajax request, let's handle it with our `Ajax` class:

```php
<?php

namespace App\Hooks;

use Themosis\Support\Facades\Ajax;

class Ajax extends Hookable
{
    public function register()
    {
        // This file is stored in app/Hooks/Ajax.php
        // This code listens for logged in and logged out users
        Ajax::listen('my-custom-action', function(){	
            // Check nonce value
            check_ajax_referer('add-posts', 'security');

            // Run custom code - Make sure to sanitize and check values before
            $result = 2 + $_POST['number'];
		
            // "Return" the result
            echo $result;

            // Close
            die();
        });
    }  
}

```

Themosis Global Object
----------------------

In the previous javascript example, we get access to some values using the following syntax:

```js
{
    url: themosis.ajaxurl
}
```

This `themosis` global JSON object is located at the end of the closing `</head>` tag of any pages. By default, it contains the key/value pair `ajaxurl` but you can easily add more key/value pairs to this global object and access them in your code.

> Note: This object is defined in the default Application hook class.

To add more values, you can use the `themosis_front_global` filter like so:

```php
Filter::add('themosis_front_global', function($data) {
    $data['myData'] = 'Some value';
    return $data;
});
```

This will output the following object:

```js
var themosis = {
    ajaxurl = 'http://www.my-domain.com/wp-admin/admin-ajax.php',
    myData = 'Some value'
};
```

### Customize object name

You can easily change the variable name of this global object. In order to do so, open the `config/assets.php` file and change the `ajax.front` property value:

```php
[
    'ajax' => [
        'front' => 'themosis'
    ]
]
```

### WordPress localize

If you need JavaScript properties for use inside your script, please check the [asset guide]({{url}}/asset) and the `localize` method.