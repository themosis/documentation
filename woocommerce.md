WooCommerce
===========

- [Introduction](#introduction)
- [Support WooCommerce](#support-woocommerce)
- [Disable WooCommerce template loader](#disable-woocommerce-template-loader)
- [Add WooCommerce routes](#add-woocommerce-routes)
- [The shop template](#the-shop-template)
- [The product template](#the-product-template)
    - [Product reviews](#product-reviews)
- [Cart, checkout and account templates](#cart-checkout-and-account-templates)
- [WooCommerce documentation](#woocommerce-documentation)

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
    'is_product_tag' => 'product_tag',
    'is_wc_endpoint_url' => 'wc_endpoint'
]
```

See [extending WordPress routes]({{url}}/routing#extending-wordpress-routes) from the routing guide for more details. For a full list of available WooCommerce conditional tags, see their [official documentation](https://docs.woocommerce.com/document/conditional-tags/).

The shop template
-----------------

As we no longer let WooCommerce loads its default templates or at least not the "root" ones, we have to generate the view for the shop archive.

WooCommerce is using action hooks to attach HTML parts. Paste the following code in a custom view (note that the code sample below is based on default theme) and store it into the `shop/archive.blade.php` file inside your theme views folder:

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

Now that the products archive is defined, add the `shop` route to your application `routes/web.php` file:

```php
Route::any('shop', function () {
    return view('shop.archive');
});
```

WooCommerce product category and tag use the same template for their archive. You can also define their routes and return the same view as for the shop like so:

```php
Route::any('product_category', function () {
    return view('shop.archive');
});

Route::any('product_tag', function () {
    return view('shop.archive');
});
```

> We recommend the use of [controller classes]({{url}}/controllers) for managing requests and avoid code duplication.

The product template
--------------------

Similar to the products archive, the single product template is a WooCommerce "root" template. In order to display a product using core WooCommerce parts, create a view with the following code (based on theme default views) and store it in `shop/single.blade.php`:

```php
@extends('layouts.main')

@section('content')
    @php(do_action('woocommerce_before_main_content'))

    @loop
        @php(do_action('woocommerce_before_single_product'))
        <div id="product-{{ Loop::id() }}" {{ wc_product_class() }}>
            @php(do_action('woocommerce_before_single_product_summary'))
            
            <div class="summary entry-summary">
                @php(do_action('woocommerce_single_product_summary'))
            </div>
            
            @php(do_action('woocommerce_after_single_product_summary'))
        </div>
        @php(do_action('woocommerce_after_single_product'))
    @endloop

    @php(do_action('woocommerce_after_main_content'))
@endsection()
```

The above code sample will display a WooCommerce product. You can also customize it depending on your project. All action hooks used for this view can be found in the WooCommerce plugin folder in the `woocommerce/templates/single-product.php` file and the `woocommerce/templates/content-single-product.php` file.

> If you need to use a sidebar as well on a single product page, directly use the `dynamic_sidebar` function like in the shop view.

Now that the single product view is ready, add the corresponding route to your application `routes/web.php` file like so (based on added route conditions):

```php
Route::any('product', function () {
    return view('shop.single');
});
```

### Product reviews

In the case where product reviews feature is enabled. You might encounter an error as WordPress is requesting a `comments.php` file. WooCommerce has its own template to handle product comments/reviews. In order to add the reviews/comments file, add a `comments_template` filter like so:

```php
Filter::add('comments_template', ['WC_Template_Loader', 'comments_template_loader']);
```

The above code will call the `comments_template_loader` method on the WooCommerce template loader class and provide support for product reviews.

Cart, checkout and account templates
------------------------------------

The cart, checkout and account templates are rendered using the existing page template. Those pages use a shortcode to display their content. You can either simply use the `page` route or if you need to customize the display of each individual pages, you can define a route for each one of them using defined route conditions.

Here is an example of a default view for managing those templates, it is based on the `pages/default.blade.php` file stored in the theme:

```php
@extends('layouts.main')

@section('content')
    @loop
        @template('parts.content', 'page')

        @if(comments_open() || get_comments_number())
            @php(comments_template('/views/comments/template.php'))
        @endif
    @endloop
@endsection
```

WooCommerce documentation
-------------------------

Here is a list of useful links from the WooCommerce official documentation:

- [Conditional tags](https://docs.woocommerce.com/document/conditional-tags/)
- [WP_Product_Query](https://github.com/woocommerce/woocommerce/wiki/wc_get_products-and-WC_Product_Query)
- [WooCommerce code reference](https://docs.woocommerce.com/wc-apidocs/)
