# Errors & Logging

- [Configuration](#configuration)
- [Handling Errors](#handling-errors)
- [HTTP Exceptions](#http-exceptions)
- [Logging](#logging)

<a name="configuration"></a>
## Configuration

Lumen is pre-configured with [Monolog](https://github.com/seldaek/monolog), a [PSR-3](http://www.php-fig.org/psr/psr-3/) compatible logger.

By default, the logger is configured to use a single log file that is stored in the `storage/logs` directory; however, you may customize this behavior as needed. Since Lumen uses the popular [Monolog](https://github.com/Seldaek/monolog) logging library, you can take advantage of the variety of handlers that Monolog offers.

### Error Detail

The amount of error detail your application displays through the browser is controlled by the `APP_DEBUG` configuration option in your `.env` configuration file.

> **Note:** For local development, you should set the `APP_DEBUG` environment variable to `true`. **In your production environment, this value should always be `false`.**

<a name="handling-errors"></a>
## Handling Errors

All exceptions are handled by the `App\Exceptions\Handler` class. This class contains two methods: `report` and `render`.

The `report` method is used to log exceptions or send them to an external service like [BugSnag](https://bugsnag.com). By default, the `report` method simply passes the exception to the base implementation on the parent class where the exception is logged. However, you are free to log exceptions however you wish. If you need to report different types of exceptions in different ways, you may use the PHP `instanceof` comparison operator:

	/**
	 * Report or log an exception.
	 *
	 * This is a great spot to send exceptions to Sentry, Bugsnag, etc.
	 *
	 * @param  \Exception  $e
	 * @return void
	 */
	public function report(Exception $e)
	{
		if ($e instanceof CustomException) {
			//
		}

		return parent::report($e);
	}

The `render` method is responsible for converting the exception into an HTTP response that should be sent back to the browser. By default, the exception is passed to the base class which generates a response for you. However, you are free to check the exception type or return your own custom response.

The `$dontReport` property of the exception handler contains an array of exception types that will not be logged. By default, exceptions resulting from 404 errors are not written to your log files. You may add other exception types to this array as needed.

<a name="http-exceptions"></a>
## HTTP Exceptions

Some exceptions describe HTTP error codes from the server. For example, this may be a "page not found" error (404), an "unauthorized error" (401) or even a developer generated 500 error. In order to return such a response, use the following:

	abort(404);

Optionally, you may provide a response:

	abort(403, 'Unauthorized action.');

This method may be used at any time during the request's lifecycle.

<a name="logging"></a>
## Logging

> **Note:** If you intend to use the `Log` facade, be sure to uncomment the `$app->withFacades()` call in your `bootstrap/app.php` file.

The Lumen and Laravel logging facilities provide a simple layer on top of the powerful [Monolog](http://github.com/seldaek/monolog) library. By default, Lumen is configured to create daily log files for your application which are stored in the `storage/logs` directory. You may write information to the log like so:

	Log::info('This is some useful information.');

	Log::warning('Something could be going wrong.');

	Log::error('Something is really going wrong.');

The logger provides the seven logging levels defined in [RFC 5424](http://tools.ietf.org/html/rfc5424): **debug**, **info**, **notice**, **warning**, **error**, **critical**, and **alert**.

An array of contextual data may also be passed to the log methods:

	Log::info('Log message', ['context' => 'Other helpful information']);

#### Resolving The Logger From The Container

If you would like to resolve an instance of the logger from the service container, you may resolve it like so:

	$logger = app('Psr\Log\LoggerInterface');

Of course, you may type-hint this dependency on a route Closure or controller to have the container automatically inject the dependency.
