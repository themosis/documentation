User
====

- [Basic usage](#basic-usage)
	- [Create a new user](#create-a-new-user)
	- [Get current user](#get-current-user)
	- [Get user by ID](#get-user-by-id)
	- [Check for a specific role](#check-for-a-specific-role)
	- [Set a role to a user](#set-a-role-to-a-user)
	- [Check user capability](#check-user-capability)
	- [Update user properties](#update-user-properties)
- [User custom fields](#user-custom-fields)
	- [Add sections](#add-sections)
	- [Add custom fields](#add-custom-fields)
	- [Validate user fields](#validate-user-fields)

Basic usage
-----------

The Themosis framework comes with a User class that gives you shortcuts to core WordPress user functions.

The User class extends the core WP_User class. So every core methods are available to your User instances as well.

### Create a new user

In order to create a new user, simply use the `make()` method like so:

```php
$user = User::make('johndoe', 'asecretpassword', 'john@doe.com');
```

This will create a new user with a username of `johndoe`, a password of  `asecretpassword` assigned to the `john@doe.com` email address.

If WordPress is able to register the user or if the user already exists, it returns a `User` instance. If there is an error, WordPress returns a `WP_Error` instance.

### Get current user

Use the `current()` method to retrieve the current logged in user:

```php
$user = User::current();
```

### Get user by ID

If you have the User ID, you can retrieve an instance of this user like so:

```php
$user = User::get(24);
```

This will return an User instance from a registered user with an ID of 24.

### Check for a specific role

Check if your user instance has a specific role:

```php
$user = User::current();

if ($user->hasRole('editor'))
{
	// Do something
}
```

### Set a role to a user

You can attach a role to a selected user:

```php
$user =  User::current();

$user->setRole('editor');
```

### Check user capability

```php
$user = User::current();

if ($user->can('edit_posts'))
{
	// Do something
}
```

### Update user properties

You can update user data by providing an array of properties. Note that the ID property is automatically fetched and inserted so you don't have to look after.

```php
$user = User::current();

$user->update([
	'user_url'    => 'http://www.somewebsite.com',
	'user_email'  => 'foo@bar.com'
]);
```

User custom fields
------------------

The User class provides methods to define custom user meta data. You can create user custom fields and whether or not to order them by sections.

### Add sections

You can define sections in order to group your user fields. You can add as many sections as you want to your users by using the `addSections()` method. Pass the method a list of sections like so:

```php
$user = User::addSections([
    Section::make('section-slug', 'Section display name'),
    Section::make('social', 'Social accounts')
]);
```

> The `addSections()` method returns a User instance. Make sure to use that instance to add your user custom fields.

The above code will add two sections to the WordPress `Add User` and `Edit Profile` screens.

### Add custom fields

In order to define custom user meta data, use the `addFields()` method and pass it an array of custom fields.

In the following example, we will add custom fields to our previously defined sections. Pass an array of custom fields to each sections defined as the array key like so:

```php
// Grab our User instance from previous code sample
// and add fields to each section.
$user->addFields([
	'section-slug'  => [
        Field::text('street', ['title' => 'Street address'])
    ],
    'social'        => [
        Field::text('facebook', ['info' => 'Your facebook page.']),
        Field::text('twitter', ['info' => 'Twitter account name.'])
    ]
]);
```

> Use the Field API to define your user custom fields just like you would with metabox custom fields and options page.

#### Add custom fields without sections

You can add user custom fields without grouping them into sections. Directly use the `addFields()` method like so:

```php
User::addFields([
    Field::text('street'),
    Field::select('country', [
        [
            'Belgium',
            'Brazil',
            'Germany'
        ]
    ]),
    Field::media('avatar')
]);
```

### Validate user fields

You can sanitize/validate user custom fields by using the `validate()` method. Make sure to use the user instance used to define your custom fields in order to validate them:

```php
$user = User::addFields([
    Field::text('twitter'),
    Field::number('age')
]);

// Validate our user instance fields...
$user->validate([
    'twitter' => ['textfield', 'min:3'],
    'age'     => ['num']
]);
```

Simply define the field name as the key and specify a list of validation/sanitization rules to it.

> Even if sections are defined, only use the custom field name in order to validate its value.

We also suggest you to always prefix your custom fields name so they don't conflict with other WordPress plugins or WordPress reserved terms.

For more information about validation rules, check the [validation guide]({{url}}/validation).