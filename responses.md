# HTTP Responses

- [Basic Responses](#basic-responses)
	- [Attaching Headers To Responses](#attaching-headers-to-responses)
	- [Attaching Cookies To Responses](#attaching-cookies-to-responses)
- [Other Response Types](#other-response-types)
	- [View Responses](#view-responses)
	- [JSON Responses](#json-responses)
	- [File Downloads](#file-downloads)
- [Redirects](#redirects)
	- [Redirecting To Named Routes](#redirecting-named-routes)
	- [Redirecting With Flashed Session Data](#redirecting-with-flashed-session-data)

<a name="basic-responses"></a>
## Basic Responses

Of course, all routes and controllers should return some kind of response to be sent back to the user's browser. Lumen provides several different ways to return responses. The most basic response is simply returning a string from a route or controller:

	$app->get('/', function () {
		return 'Hello World';
	});

The given string will automatically be converted into an HTTP response by the framework.

However, for most routes and controller actions, you will be returning a full `Illuminate\Http\Response` instance or a [view](/docs/views). Returning a full `Response` instance allows you to customize the response's HTTP status code and headers. A `Response` instance inherits from the `Symfony\Component\HttpFoundation\Response` class, providing a variety of methods for building HTTP responses:

	use Illuminate\Http\Response;

	$app->get('home', function () {
		return (new Response($content, $status))
		              ->header('Content-Type', $value);
	});

For convenience, you may also use the `response` helper:

	$app->get('home', function () {
		return response($content, $status)
		              ->header('Content-Type', $value);
	});

> **Note:** For a full list of available `Response` methods, check out its [API documentation](http://laravel.com/api/master/Illuminate/Http/Response.html) and the [Symfony API documentation](http://api.symfony.com/2.7/Symfony/Component/HttpFoundation/Response.html).

<a name="attaching-headers-to-responses"></a>
#### Attaching Headers To Responses

Keep in mind that most response methods are chainable, allowing for the fluent building of responses. For example, you may use the `header` method to add a series of headers to the response before sending it back to the user:

	return response($content)
				->header('Content-Type', $type)
				->header('X-Header-One', 'Header Value')
				->header('X-Header-Two', 'Header Value');


<a name="attaching-cookies-to-responses"></a>
#### Attaching Cookies To Responses

The `withCookie` helper method on the response instance allows you to easily attach cookies to the response. For example, you may use the `withCookie` method to generate a cookie and attach it to the response instance:

	return response($content)->header('Content-Type', $type)
                     ->withCookie('name', 'value');

The `withCookie` method accepts additional optional arguments which allow you to further customize your cookie's properties:

	->withCookie($name, $value, $minutes, $path, $domain, $secure, $httpOnly)

<a name="other-response-types"></a>
## Other Response Types

The `response` helper may be used to conveniently generate other types of response instances. When the `response` helper is called without arguments, an implementation of the `Illuminate\Contracts\Routing\ResponseFactory` contract is returned. This contract provides several helpful methods for generating responses.

<a name="view-responses"></a>
#### View Responses

If you need control over the response status and headers, but also need to return a [view](/docs/views) as the response content, you may use the `view` method:

	return response()->view('hello', $data)->header('Content-Type', $type);

Of course, if you do not need to pass a custom HTTP status code or custom headers, you may simply use the global `view` helper function.

<a name="json-responses"></a>
#### JSON Responses

The `json` method will automatically set the `Content-Type` header to `application/json`, as well as convert the given array into JSON using the `json_encode` PHP function:

	return response()->json(['name' => 'Abigail', 'state' => 'CA']);

If you would like to create a JSONP response, you may use the `json` method in addition to `setCallback`:

	return response()->json(['name' => 'Abigail', 'state' => 'CA'])
	                 ->setCallback($request->input('callback'));

<a name="file-downloads"></a>
#### File Downloads

The `download` method may be used to generate a response that forces the user's browser to download the file at the given path. The `download` method accepts a file name as the second argument to the method, which will determine the file name that is seen by the user downloading the file. Finally, you may pass an array of HTTP headers as the third argument to the method:

	return response()->download($pathToFile);

	return response()->download($pathToFile, $name, $headers);

> **Note:** Symfony HttpFoundation, which manages file downloads, requires the file being downloaded to have an ASCII file name.

<a name="redirects"></a>
## Redirects

Redirect responses are instances of the `Illuminate\Http\RedirectResponse` class, and contain the proper headers needed to redirect the user to another URL. There are several ways to generate a `RedirectResponse` instance. The simplest method is to use the global `redirect` helper method:

	$app->get('dashboard', function () {
		return redirect('home/dashboard');
	});

Sometimes you may wish to redirect the user to their previous location, for example, after a form submission that is invalid. You may do so by using the `back` method:

	$app->post('user/profile', function () {
		// Validate the request...

		return redirect()->back()->withInput();
	});

<a name="redirecting-named-routes"></a>
#### Redirecting To Named Routes

When you call the `redirect` helper with no parameters, an instance of `Illuminate\Routing\Redirector` is returned, allowing you to call any method on the `Redirector` instance. For example, to generate a `RedirectResponse` to a named route, you may use the `route` method:

	return redirect()->route('login');

If your route has parameters, you may pass them as the second argument to the `route` method:

	// For a route with the following URI: profile/{id}

	return redirect()->route('profile', [1]);

If you are redirecting to a route with an "ID" parameter that is being populated from an Eloquent model, you may simply pass the model itself. The ID will be extracted automatically:

	return redirect()->route('profile', [$user]);

<a name="redirecting-with-flashed-session-data"></a>
#### Redirecting With Flashed Session Data

> **Note:** You must [enable sessions](/docs/session) before using this feature.

Redirecting to a new URL and [flashing data to the session](/docs/session) are typically done at the same time. So, for convenience, you may create a `RedirectResponse` instance **and** flash data to the session in a single method chain. This is particularly convenient for storing status messages after an action:

	$app->post('user/profile', function () {
		// Update the user's profile...

		return redirect('dashboard')->with('status', 'Profile updated!');
	});

Of course, after the user is redirected to a new page, you may retrieve and display the flashed message from the [session](/docs/session). For example, using [Blade syntax](/docs/views):

	@if (session('status'))
		<div class="alert alert-success">
			{{ session('status') }}
		</div>
	@endif
