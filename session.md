# Session

- [Configuration](#configuration)
- [Session Usage](#session-usage)
- [Flash Data](#flash-data)
- [Database Sessions](#database-sessions)
- [Session Drivers](#session-drivers)

<a name="configuration"></a>
## Configuration

Since HTTP driven applications are stateless, sessions provide a way to store information about the user across requests. Lumen, like Laravel, ships with a variety of session back-ends available for use through a clean, unified API. Support for popular back-ends such as [Memcached](http://memcached.org), [Redis](http://redis.io), and databases is included out of the box.

The session driver is controlled by the `SESSION_DRIVER` configuration option in your `.env` file. By default, Lumen is configured to use the `memcached` session driver, which will work well for the majority of applications.

> **Note:** If you are using the `.env` file to configure your application, don't forget to uncomment the `Dotenv::load()` method in your `bootstrap/app.php` file.

Before using Redis sessions with Lumen, you will need to install the `predis/predis` package (~1.0) and `illuminate/redis` package(~5.0) via Composer.

#### Reserved Keys

The Lumen framework uses the `flash` session key internally, so you should not add an item to the session by that name.

<a name="session-usage"></a>
## Session Usage

#### Enabling The Session

> **Note:** Before using sessions, you must uncomment the middleware within the `$app->middleware()` method call in your `bootstrap/app.php` file.

#### Accessing The Session

The session may be accessed in several ways, via the HTTP request's `session` method, the `Session` facade, or the `session` helper function. When the `session` helper is called without arguments, it will return the entire session object. For example:

	session()->regenerate();

#### Storing An Item In The Session

	Session::put('key', 'value');

	session(['key' => 'value']);

#### Push A Value Onto An Array Session Value

	Session::push('user.teams', 'developers');

#### Retrieving An Item From The Session

	$value = Session::get('key');

	$value = session('key');

#### Retrieving An Item Or Returning A Default Value

	$value = Session::get('key', 'default');

	$value = Session::get('key', function() { return 'default'; });

#### Retrieving An Item And Forgetting It

	$value = Session::pull('key', 'default');

#### Retrieving All Data From The Session

	$data = Session::all();

#### Determining If An Item Exists In The Session

	if (Session::has('users')) {
		//
	}

#### Removing An Item From The Session

	Session::forget('key');

#### Removing All Items From The Session

	Session::flush();

#### Regenerating The Session ID

	Session::regenerate();

<a name="flash-data"></a>
## Flash Data

Sometimes you may wish to store items in the session only for the next request. You may do so using the `Session::flash` method:

	Session::flash('key', 'value');

#### Reflashing The Current Flash Data For Another Request

	Session::reflash();

#### Reflashing Only A Subset Of Flash Data

	Session::keep(['username', 'email']);

<a name="database-sessions"></a>
## Database Sessions

When using the `database` session driver, you will need to setup a table to contain the session items. Below is an example `Schema` declaration for the table:

	Schema::create('sessions', function($table)
	{
		$table->string('id')->unique();
		$table->text('payload');
		$table->integer('last_activity');
	});

<a name="session-drivers"></a>
## Session Drivers

The session "driver" defines where session data will be stored for each request. Lumen, like Laravel, ships with several great drivers out of the box:

- `file` - sessions will be stored in `storage/framework/sessions`.
- `cookie` - sessions will be stored in secure, encrypted cookies.
- `database` - sessions will be stored in a database used by your application.
- `memcached` / `redis` - sessions will be stored in one of these fast, cached based stores.
- `array` - sessions will be stored in a simple PHP array and will not be persisted across requests.
