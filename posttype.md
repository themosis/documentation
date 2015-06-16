PostType
========

The `PostType` class helps you to register and manipulate WordPress custom post types.

**Note**: All the code in order to customize WordPress should be stored inside the `resources/admin` directory.

1. Register a custom post type
2. Register custom status

1.Register a custom post type
-----------------------------

### Basic custom post type

In this example, we register a custom post type in order to handle a list of books:

```php
// File stored in resources/admin/books.php
PostType::make('slug-books', 'Books', 'Book')->set();
```

This will build a basic custom post type accessible in the WordPress admin. You can customize the custom post type by passing arguments to the `set()` method.

```php
PostType::make('slug-books', 'Books', 'Book')->set([
    'public'        => true,
    'menu_position' => 20,
    'supports'      => ['title'],
    'rewrite'       => false,
    'query_var'     => false
]);
```

> The arguments you pass are the same used by the WordPress [register_post_type](http://codex.wordpress.org/Function_Reference/register_post_type) function.

### Methods to register a custom post type:
#### PostType::make()

```php
PostType::make($slug, $plural-name, $singular-name);
```

* **$slug**: _string_ The custom post type slug (we take the habit to give a plural noun as it represents a "collection").
* **$plural-name**: _string_ The plural name of your custom post type.
* **$singular-name**: _string_ The singular name of your custom post type.

#### PostType->set()

```php
$postType = PostType::make('slug-books', 'Books', 'Book');

$postType->set($args);
```

* **$args**: _array_ Array of arguments in order to customize the behavior of your custom post type. Check the [WordPress Codex](http://codex.wordpress.org/Function_Reference/register_post_type) for the list of arguments you can pass to the set method.
 
**Note**: The custom post type is not registered until you call the **set()** method.

### Retrieve custom post type parameters value:

The `get()` method allows you to retrieve any parameter value from your custom post type that **you defined** in the `set()` method. Here is a list of what you can grab:

> Note: this methods replace the deprecated method $postType->getSlug(). If you use this method, make sure to update your code if you use a version of the framework higher  or equal to 1.2.0.

- **name**: The custom post type name. 
- **label**: The plural display name.
- **labels**: The custom post type labels.
- **description**: The custom post type description.
- **public**: The public value of your custom post type (boolean).
- **menu_position**: The menu position value.
- **has_archive**: The has_archive value (boolean).

Those parameters are default to all custom post type. But if you have defined other parameters and passed them through the set() method, you can grab them too.

**Example with a default parameter:**

```php
$postType = PostType::make('slug-books', 'Books', 'Book')->set();

// Code
// Return "slug-books".
$name = $postType->get('name');
```

If you try to access a parameter other than the list below and that is not defined in the set method, you'll get an error.

Now let's say we define a custom URI slug for our books custom post type like so:

```php
$postType = PostType::make('slug-books', 'Books', 'Book')->set([
	'rewrite'	=> [
		'slug'	=> 'books'
	]
]);

// We can retrieve the rewrite parameter as it is defined in the set method.
$rewriteParameters = $postType->get('rewrite');
$slug = $rewriteParameters['slug']; // return the 'books' value.
```

2.Register custom status
------------------------

The PostType API provides a method to register one or multiple custom statuses for your custom post type.

> Note: This do not work for core post types (post, page, attachment,...).

When you add custom statuses, the default statuses are removed except the `draft` one. There is no need to define a `draft` status in your list of custom statuses. This is handled automatically.

When you add custom statuses, they appear in the order you defined them. And the first defined status is the one used in order to "publish/register/post" your custom post type. So basically, the first defined status is acting like the core "Publish" button or simply is the first status to be saved for your custom post type except if you choose to draft it.

### Add custom status

In this example, keeping the books custom post type, we want to build a system to rent and sell books. So we need to register a status to say that a book is available for rent, is currently rented or for sell. Plus by getting advantage of the default draft status, you can save your work for later.

#### Add one status

The `status()` method uses the same arguments than the WordPress [register\_post\_status()](https://codex.wordpress.org/Function_Reference/register_post_status) function. But you can also pass an array of statuses.

First, let's add one custom status `rent`:

```php
// Register the custom post type first.
$books = PostType::make('slug-books', 'Books', 'Book')->set();

// Register the "rent" custom status.
$books->status('rent');
```

In the example above, one status `rent` is registered. The method assign default properties to the status, the same properties used in the [register\_post\_status()](https://codex.wordpress.org/Function_Reference/register_post_status) function:

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
	'publish_text'	=> __('Rent the book')
]);
```

The code above will change the default button text of **Publish** to **Rent the book** when a user is registering its book from the wp-admin.

#### Add multiple statuses

Using the same `status()` method on your custom post type, you can add more than one status. Just pass an array of custom statuses like so:

```php
// Register the custom post type first.
$books = PostType::make('slug-books', 'Books', 'Book')->set();

// Add our custom statuses: rent, rented, sell, sold
$books->status([
	'rent',
	'rented',
	'sell',
	'sold'
]);
```

These statuses are registered with default properties and replace the default ones. If you create a new book in the wp-admin, you should see this list of statuses inside the publish metabox: Draft, Rent, Rented, Sell and Sold.

You can also define their properties like so:

```php
$books->status[
	'rent'		=> [
		'publish_text'	=> 'Save and rent the book'
	],
	'rented'	=> [
		'publish_text'	=> 'Set the book as rented'
	],
	'sell'		=> [
		'publish_text'	=> 'Sell the book'
	],
	'sold'		=> [
		'publish_text'	=> 'Set the book as sold'
	]
];
```
The array key is the custom status name and each value is an array of status properties.

> Note: Currently the UI for custom statuses is a work-in-progress. When viewing the list of your custom post type, if you click on `Quick Edit`, it still displays core statuses.

Next
----
You can read the following documentation:

* [Metabox guide](http://framework.themosis.com/docs/metabox/)
* [Taxonomy guide](http://framework.themosis.com/docs/taxonomy/)