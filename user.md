User
====

- [Basic usage](#basic-usage)
	- [Create a new user](#create-a-new-user)
	- [Get current user](#get-current-user)
	- [Get user by ID](#get-user-by-id)
	- [Check for a specific role](#check-for-a-specific-role)
	- [Set a role to a user](#set-a-role-to-a-user)
	- [Check user capability](#check-user-capability)
	- [Update user](#update-user)
	- [Update user meta](#update-user-meta)
- [User custom fields](#user-custom-fields)
	- [Add custom fields](#add-custom-fields)
	- [Add sections](#add-sections)
	- [Validation](#validations)
	- [Retrieve value](#retrieve-value)
	- [Change prefix](#change-prefix)

Basic usage
-----------

The Themosis framework comes with a User class that gives you shortcuts to core WordPress user functions. The User class extends the core WP_User class. So every core methods are available to your User instances as well.

### Create a new user

In order to create a new user, simply use the `make()` method like so:

```php
use Themosis\Support\Facades\User;

$user = User::make('username', 'secret', 'email@company.com');
```

This will create a new user with a username of `username`, a password of  `secret` assigned to the `email@company.com` email address.

If WordPress is able to register the user it returns a `User` instance. If the user already exists it throws a `Themosis\User\Exceptions\DuplicateUserException`. If there is an error it throws a `Themosis\User\Exceptions\UserException`.

Also note that the `make` method is performing a validation on user credentials. The username and password must be at least 6 characters long. If the validation fails, it throws a `Themosis\User\Exceptions\UserException`.

> When using the `User` facade `make` method, you create each time a new instance.

### Get current user

Use the `current` method in order to retrieve the currently logged in user:

```php
use Themosis\Support\Facades\User;

$user = User::current();
```

### Get user by ID

If you have the User ID, you can retrieve an instance of this user by calling the `get` method  like so:

```php
use Themosis\Support\Facades\User;

$user = User::get(24);
```

### Check for a specific role

Check if your user instance has a specific role:

```php
$user = User::current();

if ($user->hasRole('editor')) {
    // Do something
}
```

### Set a role to a user

You can modify a user role by calling the `setRole` method like so:

```php
$user =  User::current();
$user->setRole('editor');
```

### Check user capability

You can verify if a currently logged in user has a specific capability by using the `can` method:
```php
$user = User::current();

if ($user->can('edit_posts')) {
    // Do something
}
```

### Update user

You can update properties of a user by calling the `update` method and providing as an argument an array of user properties.

Note that the `ID` property is automatically inserted so you don't have to look after. See the [wp_insert_user()](https://developer.wordpress.org/reference/functions/wp_insert_user/) function for a list of properties you can pass to the array:

```php
$user = User::current();

$user->update([
    'user_url'   => 'http://www.somewebsite.com',
    'user_email' => 'foo@bar.com'
]);
```

### Update user meta

You can also update user meta data by leveraging the `updateMetaData` method on a user instance. You can update only one user meta data with the method. The first argument is a user meta key and the second argument its value:

```php
$user = User::current();

$user->updateMetaData('first_name', 'Julien');
```

If the update fails, the framework throws a `Themosis\User\Exceptions\UserException`.

User custom fields
------------------

You can define user custom fields by leveraging the `UserField` facade class. First create a new instance using the `make` method and then use the `set` method for registration:

```php
use Themosis\Support\Facades\UserField;

UserField::make()
    ->set();
```

### Add custom fields

User custom fields are organized by sections. You can add user fields without creating a section. By default, the framework is managing a `default` section where fields, not associated with one, get sorted.

In order to define new user custom fields, you may call the `add` method and pass it a field instance like so:

```php
UserField::make()
    ->add(Field::text('favorite_color'))
    ->set();
```

You can chain multiple calls to the `add` method in order to define more user fields:

```php
UserField::make()
    ->add(Field::text('favorite_color'))
    ->add(Field::integer('age'))
    ->set();
```

### Add sections

You can also organize your user custom fields by sections. You can add as many sections as you want by passing a `Section` instance to the `add` method. A section instance requires a section ID as the first parameter, a section title as the second parameter and a list of custom fields as the third argument:

```php
use Themosis\Support\Facades\Field;
use Themosis\Support\Facades\UserField;
use Themosis\Support\Section;

UserField::make()
    ->add(new Section('social', 'Social', [
        Field::text('twitter'),
        Field::text('facebook')
    ]))
    ->set();
```

### Validation

The user field instance is using the `illuminate/validation` package in order to validate user custom fields values. Specify a `rules` option on the fields you wish to validate:

```php
UserField::make()
    ->add(Field::text('favorite_color', [
        'rules' => 'required'
    ]))
    ->add(Field::integer('age', [
        'rules' => 'numeric|min:18'
    ]))
    ->add(new Section('social', 'Social', [
        Field::text('twitter', [
            'rules' => 'url'
        ])
    ]))
    ->set();
```

See the official Laravel documentation for a [list of available validation rules](https://laravel.com/docs/validation#available-validation-rules) to use with your fields.

### Retrieve value

By default, all user fields names are prefixed by `th_`. If you define a user field with a name of `age`, it is stored inside the WordPress database table as `th_age`.

In order to retrieve a user field value, we suggest you to use the core Wordpress [get_user_meta](https://developer.wordpress.org/reference/functions/get_user_meta/) function:

```php
$user = User::current();
$age = get_user_meta($user->ID, 'th_age');
```

### Change prefix

By default all user fields names are prefixed by `th_`. You can modify the prefix by passing a `prefix` option to the user field `make` method like so:

```php
UserField::make([
    'prefix' => 'wp_'
])
    ->set();
```

If you don't want to use a prefix, pass an empty string value to the `prefix` option.