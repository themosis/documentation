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
- **capability**: _string_ Use this parameter to change the capability a user need in order to view the page.
- **icon**: _string_ Allows you to define a URL to an icon for the page menu.
- **position**: _int_ By default the page is displayed at the bottom. Specify a number/position to move your page menu up or down in the WordPress administration.
- **tabs**: _boolean_ Boolean value only. By default is set to `true`. This parameter is useful when you're using the WordPress Settings API. This parameter will tell to defined sections to behave like tabs or not.

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

Beside building custom admin pages, the Themosis framework comes with an API to easily create settings pages.

In order to create a setting page, you have to define sections and custom settings per section.

### Define sections

By default, sections are organized by tab. But you can easily alter this behavior by setting the page parameter `tabs` to false like so:

```php
Page::make('my-custom-page', 'A custom page')->set(array(
	'tabs'	=> false
));
```
> This parameter is important because if you let sections as tabs, your settings are saved with the **section slug**. If you decide to not use the tabs, your settings are saved with the **page slug**.

In order to define a section, simply use the `Section` class like so:

```php
// Add a section to an array
$sections[] = Section::make('section-slug-name', 'Section Title');
```

You can add as many sections as you want. Simply define a section slug and its name using the `Section::make()` method.

Now add the created sections to your page:

```php
$page = Page::make('page-slug', 'Page Title)->set();

// Add the previously created section(s)
$page->addSections($sections);
```
Now that we have sections, let's add settings.

### Define settings

To define settings for your page, simply use the `Field` class. In an associative array, set the `key` as the section slug name and assign an array value with associated fields/settings.

```php
// Add settings to an associative array.
$settings['section-slug-name'] = array(
	Field::text('street-address'),
	Field::text('phone'),
	Field::media('theme-logo')
);
```
The code above will add 3 settings to the section `section-slug-name`.

Now add the settings to your page like so:

```php
$page->addSettings($settings);
```

If you look in the admin, you should have an option page with one tab containing your 3 settings.

### Validate your settings



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


