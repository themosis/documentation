Page
====

- [Introduction](#introduction)
- [Basic usage](#basic-usage)
    - [Set page menu](#set-page-menu)
    - [Set page icon](#set-page-icon)
    - [Set page position](#set-page-position)
    - [Set page parent](#set-page-parent)
    - [Set page capability](#set-page-capability)
    - [Set page for network](#set-page-for-network)
- [Page routing](#page-routing)
    - [Handle GET requests](#handle-get-requests)
    - [Handle POST requests](#handle-post-requests)
- [WordPress Settings API](#wordpress-settings-api)
	- [Define sections](#define-sections)
	- [Define settings](#define-settings)
	- [Validation](#validation)
	- [Retrieve option](#retrieve-option)
	- [Change the prefix](#change-the-prefix)

Introduction
------------

The `Page` class allows you to create custom WordPress administrative pages or to create WordPress Settings pages that leverage the WordPress Settings API.

Basic usage
-----------

In order to create a WordPress administration page, you first need to create a page instance by calling the `make` method followed by the `set` method in order to register the page within WordPress:

```php
use Themosis\Support\Facades\Page;

Page::make('demo-page', 'Demo Page')
    ->set();
```

```php
Page::make($slug, $title)
    ->set();
```

* **$slug**: _string_ The page slug
* **$title**: _string_ The page display title

Like other classes, it uses a `set()` method in order to register the instance.

By default, a page is empty. In order to customize your page content, you need to leverage the [page "routing"](#page-routing) methods.

> This is not a routing system and the page API does not leverage the `illuminate/routing` package.

### Set page menu

By default the page menu title text is using the value provided as the second argument to the `make` method. But you can provide a specific menu title by using the `setMenu` method on your page instance like so:

```php
Page::make('demo-page', 'Very Long Title Rendered On Page')
    ->setMenu('Demo Page')
    ->set();
```

The page instance also provide a getter method `getMenu` if you need to retrieve the menu value in your code.

### Set page icon

You can define a page menu icon on your page instance by calling the `setIcon` method:

```php
Page::make('demo-page', 'Demo Page')
    ->setIcon('dashicons-admin-home')
    ->set();
```

A `getIcon` method is available if you need to retrieve its value.

### Set page position

In order to modify the page menu position in WordPress, you may use the `setPosition` method on your page instance:

```php
Page::make('demo-page', 'Demo')
    ->setPosition(25)
    ->set();
```

You can use the `getPosition` method to retrieve the page position value.

### Set page parent

WordPress allows you to define your page as a children page. Call the `setParent` method to define the page parent:

```php
Page::make('demo-page', 'Demo')
    ->setParent('edit.php')
    ->set();
```

You can use another page slug as a parameter or one of the WordPress core names. The above example will show the page under the "Posts" menu.

You can retrieve the parent value by using the `getParent` method on your page instance.

### Set page capability

In order to let administration users access the page, you can provide a custom capability to your page by using the `setCapability` method. By default, all pages have the `manage_options` capability.

```php
Page::make('demo-page', 'Demo')
    ->setCapability('edit_posts')
    ->set();
```

Like other setters, you can also retrieve the page capability value by calling the `getCapability` method.

### Set page for network

If the WordPress installation is configured for multisite and that the page should be used at the network level, you may use the `network` method:

```php
Page::make('demo-page', 'Demo')
    ->network()
    ->set()
```

The above code will render the page only at the multisite network level. The method accepts a boolean value as an argument and is set by default to `true`.

```php
Page::make('demo-page', 'Demo')
    ->network(false)
    ->set();
```

The above code is rendering a classic administration page, making it available on a single WordPress installation.

Page routing
----------------

The page routing system is based on the WordPress action hooks and its `action` variable mechanism for handling administration requests. The implemented page routing system abstracts the WordPress hooks for ease-of-use.

### Handle GET requests

By default, the page "routing" is configured to listen to `GET` requests. In order to define the default content of your page, you may use the `route` method and pass it the `/` value as the first parameter like so:

```php
$page = Page::make('demo-page', 'Demo Page')
    ->set();
    
$page->route('/', function () {
    return view('admin.home');
});
```

The `route` method accepts as a first argument an `action` value (`/` here) and a callback as a second argument.

On `GET` requests, the route callback must always return a view. You cannot return a string value directly. In the above example, the page default route is returning the `admin.home` view stored as the `resources/views/admin/home.blade.php` file:

```php
<!-- Demo Page - Home view -->
<h1>Demo Page - Home</h1>
<p>This is the content for the page "/" route.</p>
```

#### Use a controller class

It is also possible to use a controller class as the callback with the `route` method using the `ClassName@method` syntax. You must specify the fully qualified class name as the namespace is not handled by the API.

Here is an example for a controller stored inside the `App\Http\Controllers\Admin` directory:

```php
// App/Http/Controllers/Admin/DemoPageController.php
<?php

namespace App\Http\Controllers\Admin;

use Illuminate\Http\Request;
use App\Http\Controllers\Controller;

class DemoPageController extends Controller
{
    public function index()
    {
        return view('admin.home');
    }
}
```

The above `DemoPageController` can be used with the `route` method like so:

```php
$page->route('/', 'App\Http\Controllers\Admin\DemoPageController@index');
```

### Define custom actions

You can "listen" to different and multiple `GET` requests by specifying a custom `action` value as the first argument of a page route.

Each page route corresponds to a specific URL. In general, the default "home" URL of an administration page corresponds to the `/` parameter and looks like this:

```php
https://domain.com/cms/admin.php?page=demo-page
```

If you define a custom route `action` value of `about` to the `route` method, the URL for the page is the following:

```php
$page->route('about', 'App\Http\Controllers\Admin\DemoPageController@about');

// The page route URL
https://domain.com/cms/admin.php?page=demo-page&action=about
```

Note the `action` query variable and its corresponding value. The API is using the `action` variable to distinguish the different pages.

### Handle POST requests

The page `route` method can also listen to `POST` requests. But before digging into the options available by the API, let's first explain how WordPress is managing administration `POST` requests.

In general, WordPress performs `POST` request on the `admin-post.php` file located at the following URL:

```php
https://domain.com/cms/wp-admin/admin-post.php
```

Which can dynamically be created with the `admin_url()` WordPress helper:
```php
admin_url('admin-post.php');
```

Inside the `admin-post.php` file, WordPress is performing a few actions. The one used behind the scene by the API is the call to the following action hook:

```php
do_action("admin_post_{$action}");
``` 

WordPress is looking after a `$_POST['action']` variable on the request. So if your page must perform a `POST` request, your page form must send it to the `admin-post.php` URL and also have an input with an `action` name attribute and a custom value used by the page route API.

Here is a basic example of page `POST` route with a view containing a form that sends a request to the WordPress `admin-post.php` URL:

```php
// 1 - The default GET request containing the form.
$page->route('/', 'App\Http\Controllers\Admin\DemoPageController@index');

// 2 - The POST route listening to the action "register".
$page->route('register', 'App\Http\Controllers\Admin\DemoPageController@register', 'post');
```

> Note the third argument of the route method set to `post`.

The page view form must include an input with an `action` name attribute. Here with a hidden input:

```php
<h1>Demo Page - Home</h1>
<form action="{{ admin_url('admin-post.php') }}" method="post">
    <input type="hidden" name="action" value="demo-page_register"/>
    ...
</form>
```

To avoid conflicts between pages, the action value must follow a specific syntax and be prefixed with the page slug `PageSlug_Action`. So for a route with a `register` action, the value in our example must be `demo-page_register`.

Also note that a page view automatically receive the page instance as a variable under the `$__page` variable name. The page instance also provides a `getAction` helper method to generate the correct action value like so:

```php
...
<input type="hidden" name="action" value="{{ $__page->getAction('register') }}"/>
...
```

An administration page `POST` request should always be sent to the `admin-post.php` URL. Just like a GET request, you can attach a callback or a controller to the `route` method.

Because a `POST` request is sent to the `admin-post.php` URL, you can't return a view to the user but instead redirect to one of your page `GET` route:

```php
$page->route('register', function () {
    // Perform some code...
    // Trigger some events...
    // Then redirect the user back to the page.
    wp_safe_redirect(add_query_arg([
        'page' => 'demo-page'
    ], admin_url('admin.php')));
    exit();
}, 'post');
```

WordPress Settings API
----------------------

Beside building custom administration pages, the Themosis framework comes with an API to easily create WordPress settings pages.

In order to create a setting page, you have to define sections and custom settings per section.

### Define sections

In order to define a section, simply use the `Section` class and pass a list of sections to the page `addSections` method like so:

```php
use Themosis\Support\Section;

$page = Page::make('demo-settings', 'Settings')
    ->set();

$page->addSection([
    new Section('general', 'General'),
    new Section('social', 'Social')
]);
```

You can add as many sections as you want. Each section instance requires a section ID and section display title as parameters.

Now that we have sections, let's add settings.

### Define settings

To define settings for your page, simply call the `addSettings` method on your page instance and pass an associative array where the key is the section ID and the value is an array of fields/settings:

```php
$page->addSettings([
    'general' => [
        Field::text('title'),
        Field::textarea('comment')
    ],
    'social' => [
        Field::text('twitter')
    ]
]);
```

See the [field guide]({{url}}/field) for more information about available fields and options.

### Validation

The page API is now leveraging the `illuminate/validation` package in order to validate the page settings. Simply pass a list of validation rules by setting the `rules` option on the page fields:

```php
$page->addSettings([
    'general' => [
        Field::text('title', [
            'rules' => 'required|min:6'
        ]),
        Field::textarea('comment')
    ],
    'social' => [
        Field::text('twitter', [
            'rules' => 'required|url'
        ])
    ]
]);
```

> See the [list of available validation rules](https://laravel.com/docs/validation#available-validation-rules) on the official Laravel documentation.

The page API also displays the errors messages on top of the page.

### Retrieve option

When the API stores a setting value, the option name is automatically prefixed by the new field API. The default prefix is `th_`. So for example, if you define a field with a name of `title` like in the previous example, the value is stored under the `th_title` option inside the WordPress options database table.

In order to retrieve your option value, simply use the `get_option` WordPress function:

```php
$title = get_option('th_title');
```

The function also allows you to return a default value if the option does not exist:

```php
$title = get_option('th_title', 'Default Title');
```

### Change the prefix

The page API provides a `setPrefix` method in order to customize the prefix used to store the options:

```php
$page->setPrefix('wp_')
```

> It is best to define the prefix after defining the sections and settings of your page.


Next
----
Discover the other framework features:

* [Ajax]({{url}}/ajax)
* [Asset]({{url}}/asset)
* [PostType]({{url}}/posttype)
* [Field]({{url}}/field)
* [Metabox]({{url}}/metabox)
* [Taxonomy]({{url}}/taxonomy)
