# HTTP Requests

- [Accessing The Request](#accessing-the-request)
	- [Basic Request Information](#basic-request-information)
	- [PSR-7 Requests](#psr7-requests)
- [Retrieving Input](#retrieving-input)
	- [Old Input](#old-input)
	- [Cookies](#cookies)
	- [Files](#files)

<a name="accessing-the-request"></a>
## Accessing The Request

To obtain an instance of the current HTTP request via dependency injection, you should type-hint the `Illuminate\Http\Request` class on your controller constructor or method. The current request instance will automatically be injected by the [service container](/docs/container):

	<?php

	namespace App\Http\Controllers;

	use Illuminate\Http\Request;

	class UserController extends Controller
	{
		/**
		 * Store a new user.
		 *
		 * @param  Request  $request
		 * @return Response
		 */
		public function store(Request $request)
		{
			$name = $request->input('name');

			//
		}
	}

If your controller method is also expecting input from a route parameter, simply list your route arguments after your other dependencies. For example, if your route is defined like so:

	$app->put('user/{id}', 'UserController@update');

You may still type-hint the `Illuminate\Http\Request` and access your route parameter `id` by defining your controller method like the following:

	<?php

	namespace App\Http\Controllers;

	use Illuminate\Http\Request;

	class UserController extends Controller
	{
		/**
		 * Update the specified user.
		 *
		 * @param  Request  $request
		 * @param  int  $id
		 * @return Response
		 */
		public function update(Request $request, $id)
		{
			//
		}
	}

<a name="basic-request-information"></a>
### Basic Request Information

The `Illuminate\Http\Request` instance provides a variety of methods for examining the HTTP request for your application. The Lumen `Illuminate\Http\Request` extends the `Symfony\Component\HttpFoundation\Request` class. Here are a few more of the useful methods available on this class:

#### Retrieving The Request URI

The `path` method returns the request's URI. So, if the incoming request is targeted at `http://domain.com/foo/bar`, the `path` method will return `foo/bar`:

	$uri = $request->path();

The `is` method allows you to verify that the incoming request URI matches a given pattern. You may use the `*` character as a wildcard when utilizing this method:

	if ($request->is('admin/*')) {
		//
	}

To get the full URL, not just the path info, you may use the `url` method on the request instance:

	$url = $request->url();

#### Retrieving The Request Method

The `method` method will return the HTTP verb for the request. You may also use the `isMethod` method to verify that the HTTP verb matches a given string:

	$method = $request->method();

	if ($request->isMethod('post')) {
		//
	}

<a name="psr7-requests"></a>
### PSR-7 Requests

The PSR-7 standard specifies interfaces for HTTP messages, including requests and responses. If you would like to obtain an instance of a PSR-7 request, you will first need to install a few libraries. Lumen uses the Symfony HTTP Message Bridge component to convert typical Lumen requests and responses into PSR-7 compatible implementations:

	composer require symfony/psr-http-message-bridge

	composer require zendframework/zend-diactoros

Once you have installed these libraries, you may obtain a PSR-7 request by simply type-hinting the request type on your route or controller:

	use Psr\Http\Message\ServerRequestInterface;

	$app->get('/', function (ServerRequestInterface $request) {
		//
	});

If you return a PSR-7 response instance from a route or controller, it will automatically be converted back to a Lumen response instance and be displayed by the framework.

<a name="retrieving-input"></a>
## Retrieving Input

#### Retrieving An Input Value

Using a few simple methods, you may access all user input from your `Illuminate\Http\Request` instance. You do not need to worry about the HTTP verb used for the request, as input is accessed in the same way for all verbs.

	$name = $request->input('name');

You may pass a default value as the second argument to the `input` method. This value will be returned if the requested input value is not present on the request:

	$name = $request->input('name', 'Sally');

When working on forms with array inputs, you may use "dot" notation to access the arrays:

	$input = $request->input('products.0.name');

#### Determining If An Input Value Is Present

To determine if a value is present on the request, you may use the `has` method. The `has` method returns `true` if the value is present **and** is not an empty string:

	if ($request->has('name')) {
		//
	}

#### Retrieving All Input Data

You may also retrieve all of the input data as an `array` using the `all` method:

	$input = $request->all();

#### Retrieving A Portion Of The Input Data

If you need to retrieve a sub-set of the input data, you may use the `only` and `except` methods. Both of these methods accept a single `array` as their only argument:

	$input = $request->only('username', 'password');

	$input = $request->except('credit_card');

<a name="old-input"></a>
### Old Input

> **Note:** You must [enable sessions](/docs/session) before using this feature.

Lumen allows you to keep input from one request during the next request. This feature is particularly useful for re-populating forms after detecting validation errors. However, if you are using Lumen's included [validation services](/docs/validation), it is unlikely you will need to manually use these methods, as some of Lumen's built-in validation facilities will call them automatically.

#### Flashing Input To The Session

The `flash` method on the `Illuminate\Http\Request` instance will flash the current input to the [session](/docs/session) so that it is available during the user's next request to the application:

	$request->flash();

You may also use the `flashOnly` and `flashExcept` methods to flash a sub-set of the request data into the session:

	$request->flashOnly('username', 'email');

	$request->flashExcept('password');

#### Flash Input Into Session Then Redirect

Since you often will want to flash input in association with a redirect to the previous page, you may easily chain input flashing onto a redirect using the `withInput` method:

	return redirect('form')->withInput();

	return redirect('form')->withInput($request->except('password'));

#### Retrieving Old Data

To retrieve flashed input from the previous request, use the `old` method on the `Request` instance. The `old` method provides a convenient helper for pulling the flashed input data out of the [session](/docs/session):

	$username = $request->old('username');

Lumen also provides a global `old` helper function. If you are displaying old input within a [Blade template](/docs/views), it is more convenient to use the `old` helper:

	{{ old('username') }}

<a name="cookies"></a>
### Cookies

To force all cookies to be encrypted and signed, you will need to uncomment the EncryptCookies middleware in your bootstrap/app.php file. All signed cookies created by the Lumen and Laravel frameworks are encrypted and signed with an authentication code, meaning they will be considered invalid if they have been changed by the client.

#### Retrieving Cookies From The Request

To retrieve a cookie value from the request, you may use the `cookie` method on the `Illuminate\Http\Request` instance:

	$value = $request->cookie('name');

#### Attaching A New Cookie To A Response

Lumen provides a global `cookie` helper function which serves as a simple factory for generating new `Symfony\Component\HttpFoundation\Cookie` instances. The cookies may be attached to a `Illuminate\Http\Response` instance using the `withCookie` method:

	$response = new Illuminate\Http\Response('Hello World');

	$response->withCookie(cookie('name', 'value', $minutes));

	return $response;

To create a long-lived cookie, which lasts for five years, you may use the `forever` method on the cookie factory by first calling the `cookie` helper with no arguments, and then chaining the `forever` method onto the returned cookie factory:

	$response->withCookie(cookie()->forever('name', 'value'));

<a name="files"></a>
### Files

#### Retrieving Uploaded Files

You may access uploaded files that are included with the `Illuminate\Http\Request` instance using the `file` method. The object returned by the `file` method is an instance of the `Symfony\Component\HttpFoundation\File\UploadedFile` class, which extends the PHP `SplFileInfo` class and provides a variety of methods for interacting with the file:

	$file = $request->file('photo');

#### Verifying File Presence

You may also determine if a file is present on the request using the `hasFile` method:

	if ($request->hasFile('photo')) {
		//
	}

#### Validating Successful Uploads

In addition to checking if the file is present, you may verify that there were no problems uploading the file via the `isValid` method:

	if ($request->file('photo')->isValid())
	{
		//
	}

#### Moving Uploaded Files

To move the uploaded file to a new location, you should use the `move` method. This method will move the file from its temporary upload location (as determined by your PHP configuration) to a more permanent destination of your choosing:

	$request->file('photo')->move($destinationPath);

	$request->file('photo')->move($destinationPath, $fileName);

#### Other File Methods

There are a variety of other methods available on `UploadedFile` instances. Check out the [API documentation for the class](http://api.symfony.com/2.7/Symfony/Component/HttpFoundation/File/UploadedFile.html) for more information regarding these methods.
