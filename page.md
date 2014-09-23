Page
====

1. Introduction
2. Custom page
3. WordPress Settings API

1.Introduction
--------------

The `Page` class allows you to either create custom administrative pages that leverages the View API or either options pages that uses the WordPress Settings API combines with the Themosis framework Field API.

2.Custom page
-------------

In order to create a page, type the following code:

```php
Page::make($slug, $title, $parent = null, $view = null)->set();
```

* **$slug**: _string_ The page slug.
* **$title**: _string_ The page display title.
* **$parent**: _string_ The slug of a parent page if you wish to build a sub-page of an existing one.
* **$view**: _IRenderable_ A custom view file to customize your page.

Like other classes, it uses a `set()` method in order to register the page.

### Extra parameters for pages

The `set()` method allows you to pass extra parameters for your page. The current parameters are: `capability`, `icon`, `position`, `tabs`. 

```php
$custom = Page::make('my-page', 'Theme options')->set(array(
	'capability'	=> 'manage_options', // Default capability
	'icon'			=> 'http://website.com/wp-content/...', // An URL to an image asset
	'position'		=> 20, // Position of the page in the admin
	'tabs'			=> true // Make sections as tabs or not
));
```
- **capability**: Use this parameter to change the capability a user need in order to view the page.
- **icon**: Allows you to define a URL to an icon for the page menu.
- **position**: By default the page is displayed at the bottom. Specify a number/position to move your page menu up or down in the WordPress administration.
- **tabs**: Boolean value only. By default is set to `true`. This parameter is useful when you're using the WordPress Settings API. This parameter will tell to defined sections to behave like tabs or not.

### Create a custom page

By default, the Page class is configured to create options pages that leverages the WordPress Settings API with a pre-defined View file.

If you simply call the following code, you'll get a blank page with its title:

```php
Page::make('my-custom-page', 'A Custom Page')->set();
```

In order to create a custom page, you need to pass a View file just like for the "front-end". You can store this View file anywhere inside the `views` directory of the Themosis theme.

For example:

```php
// File is stored in /app/views/options/my-page.scout.php
<div class="wrap">
	<h1>{{{ $__page->get('title') }}}</h1>
	<ul>
		<li>Option 1</li>
		<li>Option 2</li>
	</ul>
	// Custom HTML content
</div>
```

Now pass this view into your `Page::make()` method like so:

```php
$page_view = View::make('options.my-page');

Page::make('my-custom-page', 'A Custom Page', $page_view)->set();
```
> Notice the `$__page` variable into the view. This variable is the instance of your Page. In the example above, we grabbed the title property registered with the `Page::make()` method.

3.WordPress Settings API
------------------------

### Define sections

The Themosis framework page system uses sections in order to organize your page. The framework throws an exception if you try to register it without any sections or settings.

In order to define a section, simply use the `Field` class `section` method.

```php
// We need to pass an array of section(s) to the Page::make() method.
$sections[] = Field::section($name, $extras);
```

* `$name`: _string_. The section slug/name.
* `$extras`: _array_. The extras properties. Check the [Field::text](https://github.com/themosis/documentation/blob/master/field.md) method.

### Define settings

To define settings/options for your page, use the `Field` class methods.

> At this moment, some fields still need implementation.

```php
// We need to pass an array of setting(s) to the Page::make() method.
$settings[] = Field::text('my-option', array('section' => 'my-section'));
```
> Take care of the extra parameter **section** added in the `$extras` parameter of the field method. This **section** argument links your field to a predefined section.

If you forget that **section** argument, your field/option won't be available.

### Example

Before create a page, let's define a section.

```php
// This create one section with a slug of 'payment'.
$sections = array(
	
	Field::section('payment', array('title' => 'Payment options'))

);
```

Now let's add options to this section.

```php
// It creates 3 settings attached to the 'payment' section.
$settings = array(

	Field::checkbox('enable', array('section' => 'payment')),
	Field::select('gateway', array('PayPal', 'Stripe', 'PayMill'), false, array('section' => 'payment')),
	Field::text('username', array('section' => 'payment'))

);
```
Finally, let's register the page.

```php
Page::make('Shop', 'shop-slug', $sections, $settings)->set();
```

### Other methods

#### setCap($cap):

```php
$page = Page::make($title, $slug, $sections, $settings);

$page->setCap($cap);
```

* `$cap`: _string_. By default, it uses the `manage_options` capability. But you can define another to restrict the access to the page.

#### setMenuIcon($url):

```php
$page = Page::make($title, $slug, $sections, $settings);

$page->setMenuIcon($url);
```

* `$url`: _string_. You can change the default menu icon. Simply pass the **absolute** url of your icon.

## Retrieve datas

In order to read the values stored in your custom page options, check the [option guide](https://github.com/themosis/documentation/blob/master/option.md).


Next
----

Discover the other features of the framework:

* [Ajax](https://github.com/themosis/documentation/blob/master/ajax.md)
* [Asset](https://github.com/themosis/documentation/blob/master/asset.md)
* [PostType](https://github.com/themosis/documentation/blob/master/posttype.md)
* [Field](https://github.com/themosis/documentation/blob/master/field.md)
* [Metabox](https://github.com/themosis/documentation/blob/master/metabox.md)
* [Taxonomy](https://github.com/themosis/documentation/blob/master/taxonomy.md)
* [Option](https://github.com/themosis/documentation/blob/master/option.md)
* [Helpers](https://github.com/themosis/documentation/blob/master/helpers.md)


