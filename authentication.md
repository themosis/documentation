Authentication
============

- [Introduction](#introduction)
- [Setup](#setup)
    - [Initialize migration files](#initialize-migration-files)
    - [Scaffold authentication files](#scaffold-authentication-files)
    - [Migrate the database](#migrate-the-database)
- [Authentication quickstart](#authentication-quickstart)
    - [Routing](#routing)
    - [Views](#views)
    - [Forms](#forms)
    - [Authenticating](#authenticating)
    - [Retrieving the authenticated customer](#retrieving-the-authenticated-customer)
    - [Protecting routes](#protecting-routes)
    - [Login throttling](#login-throttling)
- [Learn more](#learn-more)

Introduction
------------

The Themosis framework implements an authentication workflow configured out of the box for you. The authentication configuration file is located at `config/auth.php`, which contains documented options for tweaking the behavior of the authentication services.

The authentication system is not attached to the WordPress one. It is a separate solution to help you build customers areas on your application, outside of the WordPress administration.

The implementation uses the `illuminate/auth` package as well as the framework form API in order to build its related forms and validate data. One difference here is that the main user class is a `Customer` class. We have kept the user class name for WordPress administration users elements.

The Illuminate authentication facilities are made up of "guards" and "providers". Guards define how customers are authenticated on each request. By default, it ships with a `session` guard which maintains state using session storage and cookies.

Providers define how customers are retrieved from your persistent storage. By default, it supports Eloquent and the database query builder. However you are free to define additional providers as needed for your application.

Quick setup
-----------

The framework provides commands to help you get started authenticate customers on your application. Follow the next steps to quickly scaffold the authentication system:

### Initialize migration files

First create the `customers` migration file by running the `customer:table` command:

```bash
php console customer:table
```

If you also need the password reset mechanism for your application, run the `password:table` command as well:

```bash
php console password:table
```

### Scaffold authentication files

Next, provides all authentication necessary files into your `app` directory by calling the `make:auth` command:

```bash
php console make:auth
```

The command will update your `routes/web.php` file and add the following routes:

```php
Auth::routes();

Route::get('/settings', 'SettingsController@index')->name('settings');
```

> If you have existing routes defined, make sure to move those statements on top of your file.

### Migrate the database

Finally, run the migration by executing the `migrate` command like so:

```bash
php console migrate
```

The command will add the `customers` and `password_resets` tables in your database.

Authentication quickstart
-------------------------

The Themosis framework ships with several pre-built routes, views, forms and authentication controllers. After running the quick setup above, authentication controllers are installed under the `App\Http\Controllers\Auth` namespace, forms under the `App\Forms\Auth` namespace, routes are added to your `routes/web.php` file and views are installed in the application root `resources/views/auth` directory.

The `ForgotPasswordController` handles e-mailing links for resetting passwords, the `LoginController` handles authentication, the `RegisterController` handles new customer registration, the `ResetPasswordController` contains the logic to reset passwords and the `VerificationController` handles customer email confirmation if you specify the `verify` property on the `Auth::routes()` call like so:

```php
Auth::routes(['verify' => true]);
```

If your application doesn't need registration, you may disable it by removing the newly created `RegisterController` and modifying the route declaration:

```php
Auth::routes(['register' => false]);
```

### Routing

The provided authentication system defines default end-points for your customers in order to register, login, access their dashboard, logout and reset their password. If you define an option to verify email, a route is provided as well to handle the workflow. Here are the defined routes from the `Auth::routes()` method call:

```php
// URL: http://domain.com/auth/login
Route::get('auth/login', 'Auth\LoginController@showLoginForm')->name('login');
Route::post('auth/login', 'Auth\LoginController@login');
Route::post('auth/logout', 'Auth\LoginController@logout')->name('logout');

// URL: http://domain.com/auth/register
Route::get('auth/register', 'Auth\RegisterController@showRegistrationForm')->name('register');
Route::post('auth/register', 'Auth\RegisterController@register');

// URL: http://domain.com/password/reset
Route::get('password/reset', 'Auth\ForgotPasswordController@showLinkRequestForm')->name('password.request');
Route::post('password/email', 'Auth\ForgotPasswordController@sendResetLinkEmail')->name('password.email');

// URL: http://domain.com/password/reset/$token
Route::get('password/reset/{token}', 'Auth\ResetPasswordController@showResetForm')->name('password.reset');
Route::post('password/reset', 'Auth\ResetPasswordController@reset')->name('password.update');

// URL: http://domain.com/email/verify
Route::get('email/verify', 'Auth\VerificationController@show')->name('verification.notice');

// URL: http://domain.com/email/verify/$id
Route::get('email/verify/{id}', 'Auth\VerificationController@verify')->name('verification.verify');

// URL: http://domain.com/email/resend
Route::get('email/resend', 'Auth\VerificationController@resend')->name('verification.resend');
```

Once a customer is authenticated, the Themosis framework redirects to the `/settings` URI. A `GET` route is automatically added for you as well as a `SettingsController` inside the `app/Http/Controllers` directory:

```php
Route::get('/settings', 'SettingsController@index')->name('settings');
```

Here is the default `SettingsController` class provided by the framework:

```php
<?php

namespace App\Http\Controllers;

use App\Forms\Auth\LogoutForm;
use Illuminate\Http\Request;
use App\Http\Controllers\Controller;

class SettingsController extends Controller
{
    public function __construct()
    {
        $this->middleware('auth');
    }

    /**
     * Show the application customer dashboard.
     */
    public function index()
    {
        return view('settings.home', [
            'form' => $this->form(new LogoutForm())
        ]);
    }
}
```

### Views

As previously mentioned, the `php console make:auth` command creates all the views you need for authentication and place them in the `resources/views/auth` directory.

Views are minimal and expect at least a `layouts/main.blade.php` file in order to work. Generally the `layouts/main.blade.php` file is available on a default theme. If the default has been modified on your project, you can still create a layout inside the root `resources/views/layouts` directory if necessary.

### Forms

The `php console make:auth` command also creates all the necessary forms you need for authentication and place them in the `app/Forms/Auth` directory. Feel free to modify the default forms for your needs.

### Authenticating

Now that you have routes, forms and views setup for the included authentication controllers, you are ready to register and authenticate new customers for your application. You may access your application in a browser since the authentication controllers already contain the logic (via their traits) to authenticate existing customers and store new customers in the database.

#### Path customization

When a customer is successfully authenticated, they will be redirected to the `/settings` URI. You can customize the post-authentication redirect location by defining a `redirectTo` property on the `LoginController`, `RegisterController`, `ResetPasswordController` and the `VerificationController`.

```php
protected $redirectTo = '/';
```

Next, you should modify the `RedirectIfAuthenticated` middleware's `handle` method to use your new URI when redirecting the customer.

If the redirect path needs custom generation logic, you may define a `redirectTo` method instead of a `redirectTo` property:

```php
protected function redirectTo()
{
    return '/path';
}
```

> The `redirectTo` method will take precedence over the `$redirectTo` property.

#### Username customization

By default, the Themosis framework uses the `email` field for authentication. If you would like to customize this, you may define a `username` method on your `LoginController` like so:

```php
public function username()
{
    return 'username';
}
```

> The returned value must represent a table column name as well as the un-prefixed name of your login form input.

You also need to change the login form `App\Forms\Auth\LoginForm` so it provides an input field with a similar "username" name attribute.

#### Guard customization

You may also customize the "guard" that is used to authenticate and register customers. To get started, defined a `guard` method on your `LoginController`, `RegisterController` and `ResetPasswordController`. The method should return a guard instance:

```php
use Illuminate\Support\Facades\Auth;

public function guard()
{
    return Auth::guard('guard-name');
}
```

#### Storage customization

You can modify how new customers are stored into your database by modifying the `RegisterController` class.

The `create` method of the `RegisterController` is responsible for creating new `App\Customer` records in your database using the Eloquent ORM. You are free to modify this method according to the needs of your database.

> There is no `validator` method as validation is done automatically by the `App\Forms\Auth\RegisterForm` instance.

### Retrieving the authenticated customer

You may access the authenticated customer via the `Auth` facade:

```php
use Illuminate\Support\Facades\Auth;

// Get the currently authenticated customer
$customer = Auth::user();

// Get the currently authenticated customer's ID
$id = Auth::id();
```

Alternatively, once a customer is authenticated, you may access the authenticated customer via an `Illuminate\Http\Request` instance and its `user` method. Type-hinted classes will automatically be injected into your controller methods:

```php
<?php

namespace App\Http\Controllers;

use App\Forms\Auth\LogoutForm;
use Illuminate\Http\Request;
use App\Http\Controllers\Controller;

class DashboardController extends Controller
{
    public function __construct()
    {
        $this->middleware('auth');
    }

    /**
     * Show the application customer dashboard.
     */
    public function index(Request $request)
    {
        return view('settings.dashboard', [
            'customer' => $request->user()
        ]);
    }
}

```

#### Determining if the current customer is authenticated

To determine if the custom is already logged into your application, you may use the `check` method on the `Auth` facade, which will return `true` if the customer is authenticated:

```php
use Illuminate\Support\Facades\Auth;

if (Auth::check()) {
    // The customer is logged in...
}
```

### Protecting routes

Route middleware can be used to only allow authenticated customers to access a given route. The framework ships with an `auth` middleware, which is defined at `App\Http\Middleware\Authenticate`. Since this middleware is already registered in your HTTP kernel, all you need to do is attach the middleware to a route definition:

```php
Route::get('/settings/profile', function () {
    // Only authenticated customers may see this...
})->middleware('auth');
```

If you are using controllers, you may call the `middleware` method from the controller's constructor instead of attaching it in the route definition directly:

```php
public function __construct()
{
    $this->middleware('auth');
}
```

#### Redirecting unauthenticated customers

When the `auth` middleware detects an unauthorized customer, it will redirect the customer to the home page by default. You may modify this behavior by updating the `redirectTo` method in your `app/Htpp/Middleware/Authenticate.php` file:

```php
/**
 * @param \Illuminate\Http\Request $request
 *
 * @return string
 */
protected function redirectTo($request)
{
    return '/';
}
```

#### Specifying a guard

When attaching the `auth` middleware to a route, you may also specify which guard should be used to authenticate the customer. The guard specified should correspond to one of the keys in the `guards` array of your `config/auth.php` configuration file:

```php
public function __construct()
{
    $this->middleware('auth:api');
}
```

### Login throttling

If you are using the framework built-in `LoginController` class, the `Themosis\Core\Auth\ThrottlesLogins` trait is already included in your controller. By default, the customer will not be able to login for one minute if they fail to provide the correct credentials after several attempts. The throttling is unique to the customer's username/e-mail address and their IP address.

Learn more
----------

The Themosis framework is leveraging the `illuminate/auth` package for its authentication mechanism. For more information regarding the authentication API, please read the official documentation for details:

- [Manually authenticating users/customers](https://laravel.com/docs/authentication#authenticating-users)
- [HTTP basic authentication](https://laravel.com/docs/authentication#http-basic-authentication)
- [Logging out](https://laravel.com/docs/authentication#logging-out)
- [Social authentication](https://github.com/laravel/socialite)
- [Adding custom guards](https://laravel.com/docs/authentication#adding-custom-guards)
- [Adding custom user providers](https://laravel.com/docs/authentication#adding-custom-user-providers)
- [Events](https://laravel.com/docs/5.8/authentication#events)