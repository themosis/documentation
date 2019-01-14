WooCommerce
===========

- [Introduction](#introduction)
- [Support WooCommerce](#support-woocommerce)
- [Disable WooCommerce template loader](#disable-woocommerce-template-loader)
- [Add WooCommerce routes](#add-woocommerce-routes)
- [The shop template](#the-shop-template)
- [The product template](#the-product-template)

Introduction
------------

The Themosis framework, since version 2.0, is fully compatible with the WooCommerce plugin. This guide will help you build a compatible theme by keeping the theme file structure and the views provided by the framework.

Support WooCommerce
-------------------

In order to start building your theme for use with WooCommerce, you first need to add the theme support for it. Open the theme `config/support.php` file and add the `woocommerce` key feature:

```php
return [
    'woocommerce'
];
```

You can also add support for the WooCommerce product gallery by adding the following features:

```php
return [
    'woocommerce',
    'wc-product-gallery-zoom',
    'wc-product-gallery-lightbox',
    'wc-product-gallery-slider'
];
```

Once you add those features to your theme, WooCommerce is going to bootstrap its templates in order to build the shop and single product pages.

> Expect to see errors on screen related to missing header, footer or sidebar templates.

As the theme does not use WordPress templates, we have to tell WooCommerce to not try to use them as well. Please read below to build your shop and single product pages.

Disable WooCommerce template loader
-----------------------------------

The Themosis theme does not fully embrace WordPress templates. Instead of writing multiple template files at the theme root, the framework is only using the theme `index.php` file. The inner mechanism then allow developers to manage the display of their content through view files stored inside the theme `views` directory.

Because themes built with the Themosis framework do not provide a `header.php`, a `footer.php` and a `sidebar.php` file, we need to tell WooCommerce to not use those files when loading its templates.

In order to handle this, remove an `init` action hook and target the `WC_Template_Loader` class and its `init` method like so:

```php
<?php
// File stored in inc/woocommerce.php (for example)
Action::remove('init', ['WC_Template_Loader', 'init'], 10);
```

The above code will stop WooCommerce default templates to be loaded by the plugin. Next, let's add WooCommerce routes to the application.

Add WooCommerce routes
----------------------

In order to use WooCommerce conditional tags with the Route API, we need to update the application route `conditions` property stored inside the root `config/app.php` file:

```php
'conditions' => [
    ...,
    'is_shop' => 'shop',
    'is_product' => 'product',
    'is_cart' => 'cart',
    'is_checkout' => 'checkout',
    'is_account_page' => 'account',
    'is_product_category' => 'product_category',
    'is_product_tag' => 'product_tag'
]
```

See [extending WordPress routes]({{url}}/routing#extending-wordpress-routes) from the routing guide for more details. For a full list of available WooCommerce conditional tags, see their [official documentation](https://docs.woocommerce.com/document/conditional-tags/).

The shop template
-----------------

As we no longer let WooCommerce loads its default templates or at least not the "root" ones, we have to generate the view for the shop archive.

WooCommerce is using action hooks to attach HTML parts. Paste the following code in a custom view (note that the code sample below is based on default theme):

```php
@extends('layouts.main')

@section('content')
    @php(do_action('woocommerce_before_main_content'))
    <header class="woocommerce-products-header">
        @if(apply_filters('woocommerce_show_page_title', true))
            <h1 class="woocommerce-products-header__title page-title">{{ woocommerce_page_title(false) }}</h1>
        @endif
        @php(do_action('woocommerce_archive_description'))
    </header>
    @if(woocommerce_product_loop())
        @php(do_action('woocommerce_before_shop_loop'))
        
        {!! woocommerce_product_loop_start(false) !!}
        
        @if(wc_get_loop_prop('total'))
            @while(have_posts())
                @php(the_post())
                
                @php(do_action('woocommerce_shop_loop'))
                @php(wc_get_template_part('content', 'product'))
            @endwhile
        @endif
        
        {!! woocommerce_product_loop_end(false) !!}
        
        @php(do_action('woocommerce_after_shop_loop'))
    @else
        @php(do_action('woocommerce_no_products_found'))
    @endif

    @php(do_action('woocommerce_after_main_content'))
@endsection
```

This is a default sample on how to manage the WooCommerce products archive. You can customize it as you want for your own project. This is based on the WooCommerce template stored in the plugin at `woocommerce/templates/archive-product.php`.

In this template, we only omit the call to `do_action('woocommerce_sidebar');` as it is trying to call the WordPress `get_sidebar` function. If you need to use a sidebar on your products archive page, you can directly call the [dynamic_sidebar]() function instead in your view like so:

```php
@if(function_exists('dynamic_sidebar'))
    @php(dynamic_sidebar('sidebar-shop'))
@endif
```

The above code is displaying the registered sidebar with an identifier of `sidebar-shop`. You can register as many sidebars as you want by editing the theme `config/sidebars.php` file.

The product template
--------------------

> In progress...
