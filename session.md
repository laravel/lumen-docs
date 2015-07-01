# Session

- [Introduction](#introduction)
- [Basic Usage](#basic-usage)
	- [Flash Data](#flash-data)

<a name="introduction"></a>
## Introduction

Since HTTP driven applications are stateless, sessions provide a way to store information about the user across requests. Lumen ships with a variety of session back-ends available for use through a clean, unified API. Support for popular back-ends such as [Memcached](http://memcached.org), [Redis](http://redis.io), and databases is included out of the box.

### Enabling The Session

To enable sessions, you must uncomment all of the middleware within the `$app->middleware()` method call in your `bootstrap/app.php` file.

### Configuration

The session driver is controlled by the `SESSION_DRIVER` configuration option in your `.env` file. By default, Lumen is configured to use the `memcached` session driver, which will work well for the majority of applications. In production applications, you may consider using the `memcached` or `redis` drivers for even faster session performance.

The session driver defines where session data will be stored for each request. Lumen ships with several great drivers out of the box:

<div class="content-list" markdown="1">
- `file` - sessions are stored in `storage/framework/sessions`.
- `cookie` - sessions are stored in secure, encrypted cookies.
- `database` - sessions are stored in a database used by your application.
- `memcached` / `redis` - sessions are stored in one of these fast, cached based stores.
- `array` - sessions are stored in a simple PHP array and will not be persisted across requests.
</div>

> **Note:** The array driver is typically used for running [tests](/docs/testing) to prevent session data from persisting.

### Driver Prerequisites

#### Database

When using the `database` session driver, you will need to setup a table to contain the session items. Below is an example `Schema` declaration for the table:

	Schema::create('sessions', function ($table) {
		$table->string('id')->unique();
		$table->text('payload');
		$table->integer('last_activity');
	});

#### Redis

Before using Redis sessions with Lumen, you will need to install the `predis/predis` package (~1.0) via Composer.

### Other Session Considerations

The Lumen framework uses the `flash` session key internally, so you should not add an item to the session by that name.

<a name="basic-usage"></a>
## Basic Usage

#### Accessing The Session

First, let's access the session. We can access the session instance via the HTTP request, which can be type-hinted on a controller method. Remember, controller method dependencies are injected via the Lumen [service container](/docs/container):

	<?php

	namespace App\Http\Controllers;

	use Illuminate\Http\Request;
	use App\Http\Controllers\Controller;

	class UserController extends Controller
	{
		/**
		 * Show the profile for the given user.
		 *
		 * @param  Request  $request
		 * @param  int  $id
		 * @return Response
		 */
		public function showProfile(Request $request, $id)
		{
			$value = $request->session()->get('key');

			//
		}
	}

When you retrieve a value from the session, you may also pass a default value as the second argument to the `get` method. This default value will be returned if the specified key does not exist in the session. If you pass a `Closure` as the default value to the `get` method, the `Closure` will be executed and its result returned:

	$value = $request->session()->get('key', 'default');

	$value = $request->session()->get('key', function() {
		return 'default';
	});

If you would like to retrieve all data from the session, you may use the `all` method:

	$data = $request->session()->all();

You may also use the global `session` PHP function to retrieve and store data in the session:

	Route::get('home', function () {
		// Retrieve a piece of data from the session...
		$value = session('key');

		// Store a piece of data in the session...
		session(['key' => 'value']);
	});

#### Determining If An Item Exists In The Session

The `has` method may be used to check if an item exists in the session. This method will return `true` if the item exists:

	if ($request->session()->has('users')) {
		//
	}

#### Storing Data In The Session

Once you have access to the session instance, you may call a variety of functions to interact with the underlying data. For example, the `put` method stores a new piece of data in the session:

	$request->session()->put('key', 'value');

#### Pushing To Array Session Values

The `push` method may be used to push a new value onto a session value that is an array. For example, if the `user.teams` key contains an array of team names, you may push a new value onto the array like so:

	$request->session()->push('user.teams', 'developers');

#### Retrieving And Deleting An Item

The `pull` method will retrieve and delete an item from the session:

	$value = $request->session()->pull('key', 'default');

#### Deleting Items From The Session

The `forget` method will remove a piece of data from the session. If you would like to remove all data from the session, you may use the `flush` method:

	$request->session()->forget('key');

	$request->session()->flush();

#### Regenerating The Session ID

If you need to regenerate the session ID, you may use the `regenerate` method:

	$request->session()->regenerate();

<a name="flash-data"></a>
### Flash Data

Sometimes you may wish to store items in the session only for the next request. You may do so using the `flash` method. Method stored in the session using this method will only be available during the subsequent HTTP request, and then will be deleted. Flash data is primarily useful for short-lived status messages:

	$request->session()->flash('status', 'Task was successful!');

If you need to keep your flash data around for even more requests, you may use the `reflash` method, which will keep all of the flash data around for an additional request. If you only need to keep specific flash data around, you may use the `keep` method:

	$request->session()->reflash();

	$request->session()->keep(['username', 'email']);
