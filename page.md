Page
====

1. Page
2. Define sections
3. Define settings
4. Example
5. Other methods
6. Retrieve datas

The `Page` class allows you to easily build option pages.

```php
Page::make($title, $slug, $sections, $settings, $parent = null)->set();
```

* `$title`: _string_. The page display title.
* `$slug`: _string_. The page slug.
* `$sections`: _array_. An array of defined sections. Use the Field::section() method.
* `$settings`: _array_. An array of defined settings. Use the Field class methods.
* `$parent`: _string_. The slug of a parent page if you wish to build a sub-page of an existing one.

Like other classes, it uses a `set()` method to be registered. The `set()` method has no parameters. Simply call it and your page becomes available.

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


