User
====

The Themosis framework comes with a User class and methods that give you shortcuts to core WordPress user functions.

The User class extends the core WP_User class. So every core methods are available to your User instances.

### Create a new user

```php
$user = User::make('johndoe', 'asecretpassword', 'john@doe.com');
```

This will create a new user with a username of `johndoe` with a password of  `asecretpassword` assigned to the `john@doe.com` email address.

If WordPress is able to register the user or if the user already exists, it returns a `User` instance. If there is an error, WordPress returns a `WP_Error` instance.

### Get current user

```php
$user = User::current();
```

### Get any user by ID

If you have the User ID, you can retrieve an instance of this user like so:

```php
$user = User::get(24);
```

This will return an User instance from a registered user with an ID of 24.

### Add a user to the framework user list

By default, the framework creates a list of existing users and save their instances in an array. If for some reasons, a user is not in that list, you can add it like so:

```php
// Simply specify the user ID
$user = User::add($userId);
```

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

You can update user data by providing an array. Note that the ID property is automatically fetched and inserted so you don't have to look after.

```php
$user = User::current();

$user->update(array(
	'url'		=> 'http://www.somewebsite.com',
	'email'		=> 'foo@bar.com'
));
```