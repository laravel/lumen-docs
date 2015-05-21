# Cache

- [Configuration](#configuration)
- [Basic Usage](#basic-usage)

<a name="configuration"></a>
## Configuration

The `CACHE_DRIVER` option in your `.env` file determines the cache "driver" to be used for the application. Of course, Lumen supports the same drivers as the full-stack Laravel framework, including Memcached and Redis:

- `array`
- `file`
- `memcached`
- `redis`
- `database`

> **Note:** If you are using the `.env` file to configure your application, don't forget to uncomment the `Dotenv::load()` method in your `bootstrap/app.php` file.

### Memcached

If you are using the Memcached driver, you may also set the `MEMCACHED_HOST` and `MEMCACHED_PORT` options in your `.env` configuration file.

### Redis

Before using a Redis cache with Lumen, you will need to install the `predis/predis` (~1.0) and the `illuminate/redis` (~5.0) packages via Composer.

### Database

When using the database cache driver, you will need to setup a table to contain the cache items. You'll find an example Schema declaration for the table below:

	Schema::create('cache', function($table) {
	    $table->string('key')->unique();
	    $table->text('value');
	    $table->integer('expiration');
	});

<a name="basic-usage"></a>
## Basic Usage

> **Note:** If you intend to use the `Cache` facade, be sure to uncomment the `$app->withFacades()` call in your `bootstrap/app.php` file.

#### Storing An Item In The Cache

	Cache::put('key', 'value', $minutes);

#### Using Carbon Objects To Set Expire Time

	$expiresAt = Carbon::now()->addMinutes(10);

	Cache::put('key', 'value', $expiresAt);

#### Storing An Item In The Cache If It Doesn't Exist

	Cache::add('key', 'value', $minutes);

The `add` method will return `true` if the item is actually **added** to the cache. Otherwise, the method will return `false`.

#### Checking For Existence In Cache

	if (Cache::has('key')) {
		//
	}

#### Retrieving An Item From The Cache

	$value = Cache::get('key');

#### Retrieving An Item Or Returning A Default Value

	$value = Cache::get('key', 'default');

	$value = Cache::get('key', function() { return 'default'; });

#### Storing An Item In The Cache Permanently

	Cache::forever('key', 'value');

Sometimes you may wish to retrieve an item from the cache, but also store a default value if the requested item doesn't exist. You may do this using the `Cache::remember` method:

	$value = Cache::remember('users', $minutes, function() {
		return DB::table('users')->get();
	});

You may also combine the `remember` and `forever` methods:

	$value = Cache::rememberForever('users', function() {
		return DB::table('users')->get();
	});

Note that all items stored in the cache are serialized, so you are free to store any type of data.

#### Pulling An Item From The Cache

If you need to retrieve an item from the cache and then delete it, you may use the `pull` method:

	$value = Cache::pull('key');

#### Removing An Item From The Cache

	Cache::forget('key');
