# Errors & Logging

- [Introduction](#introduction)
- [Configuration](#configuration)
- [The Exception Handler](#the-exception-handler)
	- [Report Method](#report-method)
	- [Render Method](#render-method)
- [HTTP Exceptions](#http-exceptions)
- [Logging](#logging)

<a name="introduction"></a>
## Introduction

When you start a new Lumen project, error and exception handling is already configured for you. In addition, Lumen is integrated with the [Monolog](https://github.com/Seldaek/monolog) logging library, which provides support for a variety of powerful log handlers.

<a name="configuration"></a>
## Configuration

#### Error Detail

The amount of error detail your application displays through the browser is controlled by the `APP_DEBUG` configuration option in your `.env` configuration file.

For local development, you should set the `APP_DEBUG` environment variable to `true`. In your production environment, this value should always be `false`.

<a name="the-exception-handler"></a>
## The Exception Handler

All exceptions are handled by the `App\Exceptions\Handler` class. This class contains two methods: `report` and `render`. We'll examine each of these methods in detail.

<a name="report-method"></a>
### The Report Method

The `report` method is used to log exceptions or send them to an external service like [BugSnag](https://bugsnag.com). By default, the `report` method simply passes the exception to the base class where the exception is logged. However, you are free to log exceptions however you wish.

For example, if you need to report different types of exceptions in different ways, you may use the PHP `instanceof` comparison operator:

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

#### Ignoring Exceptions By Type

The `$dontReport` property of the exception handler contains an array of exception types that will not be logged. By default, exceptions resulting from 404 errors are not written to your log files. You may add other exception types to this array as needed.

<a name="render-method"></a>
### The Render Method

The `render` method is responsible for converting a given exception into an HTTP response that should be sent back to the browser. By default, the exception is passed to the base class which generates a response for you. However, you are free to check the exception type or return your own custom response:

    /**
     * Render an exception into an HTTP response.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Exception  $e
     * @return \Illuminate\Http\Response
     */
    public function render($request, Exception $e)
    {
    	if ($e instanceof CustomException) {
    		return response()->view('errors.custom', [], 500);
    	}

        return parent::render($request, $e);
    }

<a name="http-exceptions"></a>
## HTTP Exceptions

Some exceptions describe HTTP error codes from the server. For example, this may be a "page not found" error (404), an "unauthorized error" (401) or even a developer generated 500 error. In order to generate such a response from anywhere in your application, use the following:

	abort(404);

The `abort` method will immediately raise an exception which will be rendered by the exception handler. Optionally, you may provide the response text:

	abort(403, 'Unauthorized action.');

This method may be used at any time during the request's lifecycle.

<a name="logging"></a>
## Logging

The Lumen logging facilities provide a simple layer on top of the powerful [Monolog](http://github.com/seldaek/monolog) library. By default, Lumen is configured to create daily log files for your application which are stored in the `storage/logs` directory. You may write information to the logs using the `Log` facade:

	<?php

	namespace App\Http\Controllers;

	use Log;
	use App\Http\Controllers\Controller;

	class UserController extends Controller
	{
		/**
		 * Show the profile for the given user.
		 *
		 * @param  int  $id
		 * @return Response
		 */
		public function showProfile($id)
		{
			Log::info('Showing user profile for user: '.$id);

			return view('user.profile', ['user' => User::findOrFail($id)]);
		}
	}

The logger provides the seven logging levels defined in [RFC 5424](http://tools.ietf.org/html/rfc5424): **debug**, **info**, **notice**, **warning**, **error**, **critical**, and **alert**.

	Log::debug($error);
	Log::info($error);
	Log::notice($error);
	Log::warning($error);
	Log::error($error);
	Log::critical($error);
	Log::alert($error);

#### Contextual Information

An array of contextual data may also be passed to the log methods. This contextual data will be formatted and displayed with the log message:

	Log::info('User failed to login.', ['id' => $user->id]);
