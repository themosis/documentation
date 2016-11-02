PostType
========

- [Basic usage](#basic-usage)
	- [Plural and single display names](#plural-and-single-display-names)
	- [Customize your custom post type](#customize-your-custom-post-type)
	- [Retrieve custom post type parameters value](#retrieve-custom-post-type-parameters-value)
- [Custom status](#custom-status)
	- [Add custom status](#add-custom-status)

The `PostType` class helps you to register and manipulate WordPress custom post types.

Basic usage
-----------

In order to register a custom post type, you will call the `make` and `set` methods of the PostType class.

For example, let's register a custom post type in order to handle a list of books. The custom post type will have a slug of `books`:

```php
// File stored in resources/admin/books.php
PostType::make('books', 'Books', 'Book')->set([
    'public' => true
]);
```

This will build a basic custom post type accessible in the WordPress admin. You can customize the custom post type by passing arguments to the `set()` method. In the above code we specified that the custom post type should be public. You can pass all the arguments defined in the WordPress core function [register_post_type](https://codex.wordpress.org/Function_Reference/register_post_type#Arguments) like so:

```php
PostType::make('slug-books', 'Books', 'Book')->set([
    'public'        => true,
    'menu_position' => 20,
    'supports'      => ['title'],
    'rewrite'       => false,
    'query_var'     => false
]);
```

> Note: the custom post type will only be registered if you call the `set()` method.

### Plural and single display names

The `make()` method needs a plural and singular display names as second and third parameter. Those parameters will pre-fill the labels property of your custom post type.

```php
PostType::make('books', 'Books', 'book')->set();
```

In the above code sample, the custom post type will have a plural display name of `Books` and a singular display name of `Book`.

> Note: you can override the custom post type `labels` property by adding it to the set method array.

### Customize your custom post type

In order to define the behavior of your custom post type, use the `set()` method and pass it an array of [arguments](https://codex.wordpress.org/Function_Reference/register_post_type#Arguments) like so:

```php
PostType::make('books', 'Books', 'Book')->set([
    'public'   => false,
    'supports' => ['title', 'editor'],
    'labels'   => [
        'add_item' => __('Add', THEMOSIS_TEXTDOMAIN)
    ]
]);
```

> Note: the `THEMOSIS_TEXTDOMAIN` constant value is defined inside the `application.config.php` file.

### Retrieve custom post type parameters value

The `get()` method allows you to retrieve parameters value from your custom post type that **you defined** in the `set()` method. Here is a list of what you can grab:

> Note: this methods replace the deprecated method $postType->getSlug(). If you use this method, make sure to update your code if you use a version of the framework higher or equal to 1.2.0.

- **name**: The custom post type name. 
- **label**: The plural display name.
- **labels**: The custom post type labels.
- **description**: The custom post type description.
- **public**: The public value of your custom post type (boolean).
- **menu_position**: The menu position value.
- **has_archive**: The has_archive value (boolean).

Those parameters are default to all custom post type. But if you have defined other parameters and passed them through the set() method, you can grab them too.

Example with a default parameter:

```php
$postType = PostType::make('books', 'Books', 'Book')->set();

// Return "books".
$name = $postType->get('name');
```

> If you try to access a parameter other than the list above and that it's not defined in the set method, you'll get an error.

Now let's say we define a custom URI slug for our `books` custom post type like so:

```php
$postType = PostType::make('books', 'Books', 'Book')->set([
    'rewrite' => [
        'slug' => 'library'
    ]
]);

// We can retrieve the rewrite parameter as it is defined in the set method.
$rewriteParameters = $postType->get('rewrite');
$slug = $rewriteParameters['slug']; // return the 'library' value.
```

Custom status
-------------

The PostType API provides a method to register one or multiple custom statuses for your custom post type.

> Note: This do not work for core post types (post, page, attachment,...).

When you add custom statuses, the default statuses are removed except the `draft` one. There is no need to define a `draft` status in your list of custom statuses. This is handled automatically.

When you add custom statuses, they appear in the order you defined them. And the first defined status is the one used in order to "publish/register/post" your custom post type. So basically, the first defined status is acting like the core "Publish" button or simply is the first status to be saved for your custom post type except if you choose to draft it.

### Add custom status

In this example, keeping the books custom post type, we want to build a system to rent and sell books. So we need to register a status to say that a book is available for rent, is currently rented or for sell. Plus by getting advantage of the default draft status, you can save your work for later.

#### Add one status

The `status()` method uses the same arguments than the WordPress [register_post_status()](https://codex.wordpress.org/Function_Reference/register_post_status) function. But you can also pass an array of statuses.

First, let's add one custom status `rent`:

```php
// Register the custom post type first.
$books = PostType::make('books', 'Books', 'Book')->set();

// Register the "rent" custom status.
$books->status('rent');
```

In the example above, one status `rent` is registered. The method assign default properties to the status, the same properties used in the [register_post_status()](https://codex.wordpress.org/Function_Reference/register_post_status) function:

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
    'publish_text' => __('Rent the book', THEMOSIS_TEXTDOMAIN)
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

> Note: Currently the UI for custom statuses is a work-in-progress. When viewing the list of your custom post type, if you click on `Quick Edit`, it still displays core statuses.

Next
----
You can read the following documentation:

* [Metabox guide]({{url}}/metabox)
* [Taxonomy guide]({{url}}/taxonomy)