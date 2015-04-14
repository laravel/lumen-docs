# Authentication

- [Introduction](#introduction)
- [Configuration](#configuration)
- [Basic Usage](#basic-usage)

<a name="introduction"></a>
## Introduction

Lumen is primarily designed for building fast micro-services and APIs; however, if you wish, you may use Laravel's authentication system to authenticate users of your Lumen application.

<a name="configuration"></a>
## Configuration

> **Note:** Using the authentication system will require enabling sessions. You can do so by uncommenting the middleware listed in the default call to `$app->middleware` in your `bootstrap/app.php` file.

The authentication system has several configuration options you can set in your `.env` file:

- `AUTH_DRIVER`
- `AUTH_MODEL`
- `AUTH_TABLE`

The `AUTH_DRIVER` value specifies the authentication driver used by the framework. If `eloquent` is specified as the driver, the Eloquent ORM driver will be utilized, while `database` will specify that the plain "database" driver should be used.

The `AUTH_MODEL` option specifies the name of the Eloquent model to be used for authentication. This model must implement the `Illuminate\Contracts\Auth\Authenticatable` contract. For an example model, check out the `App\User` model included in the full-stack Laravel framework.

The `AUTH_TABLE` option specifies which databse table contains the "users" of your application. Of course, this option only applies when using the `database` authentication driver.

<a name="basic-usage"></a>
## Basic Usage

Unlike Laravel, Lumen does not include any scaffolding for authentication, so you will need to use the authentication libraries manually.

> **Note:** If you intend to use the `Auth` facade, be sure to uncomment the `$app->withFacades()` call in your `bootstrap/app.php` file.

First, let's check out the `attempt` method:

	use Illuminate\Http\Request;

	$app->post('auth/login', function(Request $request) {

		if (Auth::attempt($request->only('email', 'password'))) {
			return redirect('dashboard');
		}

	});

The `attempt` method accepts an array of key / value pairs as its first argument. The `password` value will be [hashed](/docs/hashing). The other values in the array will be used to find the user in your database table. So, in the example above, the user will be retrieved by the value of the `email` column. If the user is found, the hashed password stored in the database will be compared with the hashed `password` value passed to the method via the array. If the two hashed passwords match, a new authenticated session will be started for the user.

The `attempt` method will return `true` if authentication was successful. Otherwise, `false` will be returned.

> **Note:** In this example, `email` is not a required option, it is merely used as an example. You should use whatever column name corresponds to a "username" in your database.

#### Authenticating A User With Conditions

You also may add extra conditions to the authentication query:

	if (Auth::attempt(['email' => $email, 'password' => $password, 'active' => 1]))	{
		// The user is active, not suspended, and exists.
	}

#### Determining If A User Is Authenticated

To determine if the user is already logged into your application, you may use the `check` method:

	if (Auth::check()) {
		// The user is logged in...
	}

#### Authenticating A User And "Remembering" Them

If you would like to provide "remember me" functionality in your application, you may pass a boolean value as the second argument to the `attempt` method, which will keep the user authenticated indefinitely, or until they manually logout. Of course, your `users` table must include the string `remember_token` column, which will be used to store the "remember me" token.

	if (Auth::attempt(['email' => $email, 'password' => $password], $remember)) {
		// The user is being remembered...
	}

If you are "remembering" users, you may use the `viaRemember` method to determine if the user was authenticated using the "remember me" cookie:

	if (Auth::viaRemember()) {
		//
	}

#### Authenticating Users By ID

To log a user into the application by their ID, use the `loginUsingId` method:

	Auth::loginUsingId(1);

#### Validating User Credentials Without Login

The `validate` method allows you to validate a user's credentials without actually logging them into the application:

	if (Auth::validate($credentials)) {
		//
	}

#### Logging A User In For A Single Request

You may also use the `once` method to log a user into the application for a single request. No sessions or cookies will be utilized:

	if (Auth::once($credentials)) {
		//
	}

#### Manually Logging In A User

If you need to log an existing user instance into your application, you may call the `login` method with the user instance:

	Auth::login($user);

This is equivalent to logging in a user via credentials using the `attempt` method.

#### Logging A User Out Of The Application

	Auth::logout();

