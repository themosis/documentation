Widgets
=======

The Themosis framework provides a folder where you can store your WordPress widgets classes.

You can create as many widgets as you want and save them inside the `app/widgets` directory of your `themosis-theme` theme.

Here is an example of a custom widget:

```php
// This file is stored in app/widgets/MyCustomWidget.php

<?php

class MyCustomWidget_Widget extends WP_Widget
{

	public function __construct(){

		$params = array(

			'description' 	=> 'The widget description',
			'name'			=> 'My Custom Widget'
	
		);

		parent::__construct('MyCustomWidget_Widget', '', $params);

	}

}

?>
```

The class name must be followed by `_Widget` in order to work and your file name should be the same as the class name without the `_Widget` suffix.

> It is preferred to use camel case syntax for your class names.

Now you should have your custom widget available inside your WordPress administration.