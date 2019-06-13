PostType
========

- [Basic usage](#basic-usage)
    - [Labels](#labels)
    - [Customize](#customize)
    - [Retrieve parameter value](#retrieve-parameter-value)
    - [The WP_Post_Type instance](#the-wp-post-type-instance)
    - [Set title placeholder](#set-title-placeholder)
- [Service container](#service-container)
- [Custom status](#custom-status)
	- [Add custom status](#add-custom-status)

Basic usage
-----------

The `PostType` class helps you register and manage WordPress custom post types.

In order to register a custom post type, you may call the `make` and `set` methods of the PostType class.

For example, let's register a custom post type in order to handle a list of books. The custom post type will have a slug of `books` and is registered inside a `Books` hookable class:

```php
<?php
namespace App\Hooks;

use Themosis\Hook\Hookable;
use Themosis\Support\Facades\PostType;

class Books extends Hookable
{
    public function register()
    {
        PostType::make('books', 'Books', 'Book')->set();
    }
}
```

This will build a basic custom post type accessible in the WordPress administration.

> Note: the custom post type will only be registered if you call the `set()` method.  
> Note: do not forget to register the new Hook in your configuration file `config/app.php`

### Labels

The `make()` method needs a plural and singular display names as second and third parameter. Those parameters will pre-fill the labels property of your custom post type.

```php
PostType::make('books', 'Books', 'book')->set();
```

In the above code sample, the custom post type will have a plural display name of `Books` and a singular display name of `Book`.

> Labels for custom post type are set in english by default.

If you need to change default labels or provide labels in your locale, you can override them by using the `setLabels()` method like so:

```php
PostType::make('books', 'Books', 'Book')
    ->setLabels([
        'add_new_item' => _x('Add a book', 'post_type', 'textdomain')
    ])
    ->set();
```

### Customize

You can customize the custom post type by passing arguments to the `setArguments()` method. You can pass all the arguments defined in the WordPress core function [register_post_type](https://developer.wordpress.org/reference/functions/register_post_type/) like so:

```php
PostType::make('slug-books', 'Books', 'Book')
    ->setArguments([
        'public' => true,
        'menu_position' => 20,
        'supports' => ['title', 'editor'],
        'rewrite' => false,
        'query_var' => false
    ])
    ->set();
```

### Retrieve parameter value

#### Get the custom post type name

If you need to retrieve the name of your custom post type instance, you can use the `getName()` or `getSlug()` method:

```php
$books = PostType::make('slug_books', 'Books', 'Book')
    ->set();

$name = $books->getName();
```

#### Get the custom post type argument

You can retrieve any value from your custom post type arguments. In order to retrieve all defined arguments, you may use the `getArguments()` method:

```php
$args = $postType->getArguments();
```

or you can fetch a single argument using the `getArgument()` method like so:

```php
$arg = $postType->getArgument('menu_position');
```

> This can only fetch a value from a defined argument when registering the custom post type.

#### Get the custom post type label

In order to fetch all labels, you can use the `getLabels()` method on your custom post type instance:

```php
$labels = $postType->getLabels();
```

or you can get a single label using the `getLabel()` method like so:

```php
$label = $postType->getLabel('view_item');
```

### The WP_Post_Type instance

WordPress now returns an object when registering a custom post type. In order to get access to this instance, use the `getInstance()` method like so:

```php
$books = PostType::make('books', 'Books', 'Book')->set();
$type = $books->getInstance(); // return WP_Post_Type instance
```

### Set title placeholder

You can define a custom title input placeholder value by calling the `setTitlePlaceholder` method on your post type instance like so:

```php
PostType::make('books', 'Books', 'Book')
    ->setTitlePlaceholder('Insert the book title here...')
    ->set();
```

Service container
-----------------

Each defined custom post type instance is registered into the application service container using the abstract name `themosis.posttype.slug`. If you need your post type instance elsewhere in your project, you may use the `app()` function helper or the `App` facade.

Here is an example based on the above `books` custom post type:

```php
PostType::make('books', 'Livres', 'Livre')->set();

$instance = app('themosis.posttype.books');

$instance = App::make('themosis.posttype.books');
```

Custom status
-------------

The PostType class provides a method to register one or multiple custom statuses for your custom post type.

> Note: This do not work for core post types (post, page, attachment,...).

When you add custom statuses, the default statuses are removed except the `draft` and `trash` ones.

When you add custom statuses, they appear in the order you defined them. And the first defined status is the one used in order to "publish/register/post" your custom post type. So basically, the first defined status is acting like the core "Publish" button or simply is the first status to be saved for your custom post type except if you choose to draft it.

### Add custom status

In this example, keeping the books custom post type, we want to build a system to rent and sell books. So we need to register a status to say that a book is available for rent, is currently rented or for sell. Plus by getting advantage of the default draft status, you can save your work for later.

#### Add one status

The `status()` method uses the same arguments than the WordPress [register_post_status()](https://developer.wordpress.org/reference/functions/register_post_status/) function. But you can also pass an array of statuses.

First, let's add one custom status `rent`:

```php
// Register the custom post type first.
$books = PostType::make('books', 'Books', 'Book')->set();

// Register the "rent" custom status.
$books->status('rent');
```

In the example above, one status `rent` is registered. The method assign default properties to the status, the same properties used in the [register_post_status()](https://developer.wordpress.org/reference/functions/register_post_status/) function:

- **label**: Its default value is the status `name` with first character set to uppercase
- **public**: Default to `true`
- **exclude\_from\_search**: Default to `false`
- **show\_in\_admin\_all\_list**: Default to `true`
- **show\_in\_admin\_status\_list**: Default to `true`
- **label\_count**: Default to `_n_noop()` function with status label
- **publish\_text**: Property available to the framework only. Default value to `__('Apply Changes')`. This property allows you to define a custom text for the **publish button** and per status.

In the following example, we add a custom publish button text to our `rent` status:

```php
$books->status('rent', [
    'publish_text' => __('Rent the book', 'textdomain')
]);
```

The code above will change the default button text of **Publish** to **Rent the book** when a user is registering its book from the edit screen.

#### Add multiple statuses

Using the same `status()` method on your custom post type, you can add more than one status. Just pass an array of custom statuses like so:

```php
// Register the custom post type first.
$books = PostType::make('books', 'Books', 'Book')->set();

// Add our custom statuses: rent, rented, sell, sold
$books->status([
    'rent',
    'rented',
    'sell',
    'sold'
]);
```

These statuses are registered with default properties. If you create a new book in the WordPress administration, you should see this list of statuses inside the publish metabox: _Draft_, _Rent_, _Rented_, _Sell_ and _Sold_.

You can also define their properties like so:

```php
$books->status([
    'rent' => [
        'publish_text' => 'Save and rent the book'
    ],
    'rented' => [
        'publish_text' => 'Set the book as rented'
    ],
    'sell' => [
        'publish_text' => 'Sell the book'
    ],
    'sold' => [
        'publish_text' => 'Set the book as sold'
    ]
]);
```

The array key is the custom status name and each value is an array of status properties.

Next
----
You can read the following documentation:

* [Metabox guide]({{url}}/metabox)
* [Taxonomy guide]({{url}}/taxonomy)
