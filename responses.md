# HTTP Responses

- [Basic Responses](#basic-responses)
    - [Attaching Headers To Responses](#attaching-headers-to-responses)
- [Other Response Types](#other-response-types)
    - [JSON Responses](#json-responses)
    - [File Downloads](#file-downloads)
- [Redirects](#redirects)
    - [Redirecting To Named Routes](#redirecting-named-routes)

<a name="basic-responses"></a>
## Basic Responses

Of course, all routes and controllers should return some kind of response to be sent back to the user's browser. Lumen provides several different ways to return responses. The most basic response is simply returning a string from a route or controller:

    $router->get('/', function () {
        return 'Hello World';
    });

The given string will automatically be converted into an HTTP response by the framework.

#### Response Objects

However, for most routes and controller actions, you will be returning a full `Illuminate\Http\Response` instance. Returning a full `Response` instance allows you to customize the response's HTTP status code and headers. A `Response` instance inherits from the `Symfony\Component\HttpFoundation\Response` class, providing a variety of methods for building HTTP responses:

    use Illuminate\Http\Response;

    $router->get('home', function () {
        return (new Response($content, $status))
                      ->header('Content-Type', $value);
    });

For convenience, you may also use the `response` helper:

    $router->get('home', function () {
        return response($content, $status)
                      ->header('Content-Type', $value);
    });

> **Note:** For a full list of available `Response` methods, check out its [API documentation](http://laravel.com/api/master/Illuminate/Http/Response.html) and the [Symfony API documentation](http://api.symfony.com/3.0/Symfony/Component/HttpFoundation/Response.html).

<a name="attaching-headers-to-responses"></a>
#### Attaching Headers To Responses

Keep in mind that most response methods are chainable, allowing for the fluent building of responses. For example, you may use the `header` method to add a series of headers to the response before sending it back to the user:

    return response($content)
                ->header('Content-Type', $type)
                ->header('X-Header-One', 'Header Value')
                ->header('X-Header-Two', 'Header Value');

Or, you may use the `withHeaders` method to specify an array of headers to be added to the response:

    return response($content)
                ->withHeaders([
                    'Content-Type' => $type,
                    'X-Header-One' => 'Header Value',
                    'X-Header-Two' => 'Header Value',
                ]);

<a name="other-response-types"></a>
## Other Response Types

The `response` helper may be used to conveniently generate other types of response instances. When the `response` helper is called without arguments, an implementation of the `Illuminate\Contracts\Routing\ResponseFactory` contract is returned. This contract provides several helpful methods for generating responses.

<a name="json-responses"></a>
#### JSON Responses

The `json` method will automatically set the `Content-Type` header to `application/json`, as well as convert the given array into JSON using the `json_encode` PHP function:

    return response()->json(['name' => 'Abigail', 'state' => 'CA']);

You can optionally provide a status code and an array of additional headers:

    return response()->json(['error' => 'Unauthorized'], 401, ['X-Header-One' => 'Header Value']);

If you would like to create a JSONP response, you may use the `json` method in addition to `setCallback`:

    return response()
                ->json(['name' => 'Abigail', 'state' => 'CA'])
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

    $router->get('dashboard', function () {
        return redirect('home/dashboard');
    });

<a name="redirecting-named-routes"></a>
#### Redirecting To Named Routes

When you call the `redirect` helper with no parameters, an instance of `Laravel\Lumen\Http\Redirector` is returned, allowing you to call any method on the `Redirector` instance. For example, to generate a `RedirectResponse` to a named route, you may use the `route` method:

    return redirect()->route('login');

If your route has parameters, you may pass them as the second argument to the `route` method:

    // For a route with the following URI: profile/{id}

    return redirect()->route('profile', ['id' => 1]);

If you are redirecting to a route with an "ID" parameter that is being populated from an Eloquent model, you may simply pass the model itself. The ID will be extracted automatically:

    return redirect()->route('profile', [$user]);
