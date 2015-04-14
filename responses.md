# HTTP Responses

- [Basic Responses](#basic-responses)
- [Redirects](#redirects)
- [Other Responses](#other-responses)

<a name="basic-responses"></a>
## Basic Responses

#### Returning Strings From Routes

The most basic response from a Lumen route is a string:

	$app->get('/', function() {
		return 'Hello World';
	});

#### Creating Custom Responses

However, for most routes and controller actions, you will be returning a full `Illuminate\Http\Response` instance or a [view](/docs/views). Returning a full `Response` instance allows you customize the response's HTTP status code and headers. A `Response` instance inherits from the `Symfony\Component\HttpFoundation\Response` class, providing a variety of methods for building HTTP responses:

	use Illuminate\Http\Response;

	return (new Response($content, $status))
	              ->header('Content-Type', $value);

For convenience, you may also use the `response` helper:

	return response($content, $status)
	              ->header('Content-Type', $value);

> **Note:** For a full list of available `Response` methods, check out its [API documentation](http://laravel.com/api/master/Illuminate/Http/Response.html) and the [Symfony API documentation](http://api.symfony.com/2.6/Symfony/Component/HttpFoundation/Response.html).

<a name="redirects"></a>
## Redirects

Redirect responses are typically instances of the `Illuminate\Http\RedirectResponse` class, and contain the proper headers needed to redirect the user to another URL.

#### Returning A Redirect

There are several ways to generate a `RedirectResponse` instance. The simplest method is to use the `redirect` helper method. When testing, it is not common to mock the creation of a redirect response, so using the helper method is almost always acceptable:

	return redirect('user/login');

#### Returning A Redirect With Flash Data

> **Note:** Before using flash data, you will need to [enable sessions](/docs/session#session-usage).

Redirecting to a new URL and [flashing data to the session](/docs/master/session) are typically done at the same time. So, for convenience, you may create a `RedirectResponse` instance **and** flash data to the session in a single method chain:

	return redirect('user/login')->with('message', 'Login Failed');

#### Redirecting To The Previous URL

You may wish to redirect the user to their previous location, for example, after a form submission. You can do so by using the `back` method:

	return redirect()->back();

	return redirect()->back()->withInput();

#### Returning A Redirect To A Named Route

When you call the `redirect` helper with no parameters, an instance of `Illuminate\Routing\Redirector` is returned, allowing you to call any method on the `Redirector` instance. For example, to generate a `RedirectResponse` to a named route, you may use the `route` method:

	return redirect()->route('login');

#### Returning A Redirect To A Named Route With Parameters

If your route has parameters, you may pass them as the second argument to the `route` method.

	// For a route with the following URI: profile/{id}

	return redirect()->route('profile', ['id' => 1]);

If you are redirecting to a route with an "ID" parameter that is being populated from an Eloquent model, you may simply pass the model itself. The ID will be extracted automatically:

	return redirect()->route('profile', ['id' => $user]);

#### Returning A Redirect To A Named Route Using Named Parameters

	// For a route with the following URI: profile/{user}

	return redirect()->route('profile', ['user' => 1]);

<a name="other-responses"></a>
## Other Responses

The `response` helper may be used to conveniently generate other types of response instances.

#### Creating A JSON Response

The `json` method will automatically set the `Content-Type` header to `application/json`:

	return response()->json(['name' => 'Abigail', 'state' => 'CA']);

#### Creating A JSONP Response

	return response()->json(['name' => 'Abigail', 'state' => 'CA'])
	                 ->setCallback($request->input('callback'));

#### Creating A File Download Response

	return response()->download($pathToFile);

	return response()->download($pathToFile, $name, $headers);

	return response()->download($pathToFile)->deleteFileAfterSend(true);

> **Note:** Symfony HttpFoundation, which manages file downloads, requires the file being downloaded to have an ASCII file name.
