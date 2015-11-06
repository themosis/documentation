Page
====

- Introduction
- Custom page
	- Extra parameters
	- Create a custom page
- WordPress Settings API
	- Define sections
	- Define settings
	- Validate your settings
	- Retrieve data

Introduction
------------

The `Page` class allows you to either create custom administrative pages that leverages the View API or either options pages that uses the WordPress Settings API combines with the Themosis framework Field API.

Custom page
-----------

In order to create a page, type the following code:

```php
Page::make($slug, $title, $parent = null, $view = null)->set();
```

* **$slug**: _string_ The page slug.
* **$title**: _string_ The page display title.
* **$parent**: _string_ The slug of a parent page if you wish to build a sub-page of an existing one.
* **$view**: _IRenderable_ A custom view file to customize your page.

Like other classes, it uses a `set()` method in order to register the page.

### Extra parameters

The `set()` method allows you to pass extra parameters for your page. The current parameters are: `capability`, `icon`, `position`, `tabs`. 

```php
$custom = Page::make('my-page', 'Theme options')->set([
	'capability'	=> 'manage_options',
	'icon'			=> 'dashicons-admin-site',
	'position'		=> 20,
	'tabs'			=> true
]);
```
- **capability**: _string_ Use this parameter to change the capability a user need in order to **view** the page.
- **icon**: _string_ Allows you to define a URL to an icon for the page menu or a [dashicon name](https://developer.wordpress.org/resource/dashicons/).
- **position**: _int_ By default the page is displayed at the bottom. Specify a number/position to move your page menu up or down in the WordPress administration.
- **tabs**: _boolean_ Boolean value only. By default is set to `true`. This parameter is useful when you're using the WordPress Settings API. This parameter will tell to defined sections to behave like tabs or not.

### Create a custom page

By default, the Page class is configured to create options pages that leverages the WordPress Settings API with a pre-defined View file.

If you simply call the following code, you'll get a blank page with its title:

```php
Page::make('my-custom-page', 'A Custom Page')->set();
```

In order to create a custom page, you need to pass a View file just like for the "front-end". You can store this View file anywhere inside the `views` directory of the `themosis-theme`.

For example:

```php
// File is stored in /resources/views/options/my-page.scout.php
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

Page::make('my-custom-page', 'A Custom Page', null, $page_view)->set();
```
> Notice the `$__page` variable into the view. This variable is the instance of your Page. In the example above, we grabbed the title property registered with the `Page::make()` method.

You can also take advantage of the View composer method to execute code only when your custom page view is rendered and pass it extra data. Check the [view guide](http://framework.themosis.com/docs/views/) for more information.

WordPress Settings API
----------------------

Beside building custom admin pages, the Themosis framework comes with an API to easily create settings pages.

In order to create a setting page, you have to define sections and custom settings per section.

### Define sections

By default, sections are organized by tab. But you can easily alter this behavior by setting the page parameter `tabs` to `false` like so:

```php
Page::make('my-custom-page', 'A custom page')->set([
	'tabs'	=> false
]);
```

> This parameter is important because if you use sections as tabs, your settings are saved with the **section slug**. If you decide to not use the tabs, your settings are saved with the **page slug**.

In order to define a section, simply use the `Section` class like so:

```php
// Add a section to an array
$sections[] = Section::make('section-slug-name', 'Section Title');
```

You can add as many sections as you want. Simply define a section slug and its name/title using the `Section::make()` method.

Now add the created sections to your page by calling the page `addSections()` method:

```php
$page = Page::make('page-slug', 'Page Title')->set();

// Add the previously created section(s).
$page->addSections($sections);
```

Now that we have sections, let's add settings.

### Define settings

To define settings for your page, simply use the `Field` class. In an associative array, set the `key` as the section slug name and assign an array value with associated fields/settings.

```php
$settings['section-slug-name'] = [
	Field::text('street-address'),
	Field::text('phone'),
	Field::media('theme-logo')
];
```

The code above will add 3 settings to the section `section-slug-name`.

Now add the settings to your page by calling the page `addSettings()` method like so:

```php
$page->addSettings($settings);
```

If you look in the admin, you should have an option page with one tab containing your 3 settings.

### Validate your settings

You can define validation/sanitized rules to your settings page. Simply call the `validate()` method on your page like so:

```php
$page->validate([
	'street-address'	=> ['textfield'],
	'phone'			=> ['num', 'min:7'],
	'theme-logo'		=> ['file:jpeg, jpg, png, gif']
));
```

You only need to specify the `setting name` as the key and the value is a list of sanitization rules.

> Please refer to the [validation guide](http://framework.themosis.com/docs/validation/) for available rules.

### Retrieve data

In order to read the values stored in your custom page options, check the [option guide](http://framework.themosis.com/docs/option/).


Next
----
Discover the other features of the framework:

* [Ajax](http://framework.themosis.com/docs/ajax/)
* [Asset](http://framework.themosis.com/docs/asset/)
* [PostType](http://framework.themosis.com/docs/posttype/)
* [Field](http://framework.themosis.com/docs/field/)
* [Metabox](http://framework.themosis.com/docs/metabox/)
* [Taxonomy](http://framework.themosis.com/docs/taxonomy/)
* [Option](http://framework.themosis.com/docs/option/)
* [Helpers](http://framework.themosis.com/docs/helpers/)