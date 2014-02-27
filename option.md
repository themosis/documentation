Option
======

The `Option` class is an helper in order to retrieve datas of option fields registered with a `Page` class.

1. Option class
2. Example

## 1. The Option class methods

The `Option` class has only one method. The `get` method helps you retrieve your option value.

### get($section, $name)

```php
Option::get($section, $name);
```

* `$section`: _string_. The section slug name.
* `$name`: _string_. The setting name.

This methods returns whatever value was saved.

## 2. Example

Let's take the 'shop' option page from the [page guide](https://github.com/themosis/documentation/blob/master/page.md).

```php
// The section
$sections = array(
	
	Field::section('payment', array('title' => 'Payment options'))

);

// The settings
$settings = array(

	Field::checkbox('enable', array('section' => 'payment')),
	Field::select('gateway', array('PayPal', 'Stripe', 'PayMill'), false, array('section' => 'payment')),
	Field::text('username', array('section' => 'payment'))

);

// The page
Page::make('Shop', 'shop-slug', $sections, $settings)->set();
```

Now, let's retrieve the settings value.

```php
$isEnabled = Option::get('payment', 'enable');

$gateway = Option::get('payment', 'gateway');

$username = Option::get('payment', 'username');
```

> The value returned may be empty. Always check your values before proceeding any further.

Next
----

Discover the other features of the framework:

* [Ajax](https://github.com/themosis/documentation/blob/master/ajax.md)
* [Asset](https://github.com/themosis/documentation/blob/master/asset.md)
* [PostType](https://github.com/themosis/documentation/blob/master/posttype.md)
* [Field](https://github.com/themosis/documentation/blob/master/field.md)
* [Metabox](https://github.com/themosis/documentation/blob/master/metabox.md)
* [Taxonomy](https://github.com/themosis/documentation/blob/master/taxonomy.md)
* [Page](https://github.com/themosis/documentation/blob/master/page.md)
* [Helpers](https://github.com/themosis/documentation/blob/master/helpers.md)

