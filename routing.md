# HTTP Routing

- [Basic Routing](#basic-routing)
- [Route Parameters](#route-parameters)
- [Named Routes](#named-routes)
- [Route Groups](#route-groups)
- [Route Prefixing](#route-prefixing)
- [CSRF Protection](#csrf-protection)
- [Method Spoofing](#method-spoofing)
- [Throwing 404 Errors](#throwing-404-errors)

<a name="basic-routing"></a>
## Basic Routing

You will define most of the routes for your application in the `app/Http/routes.php` file, which is loaded by the `bootstrap/app.php` file. Like Laravel, the most basic Lumen routes simply accept a URI and a `Closure`:

#### Basic GET Route

	$app->get('/', function() {
		return 'Hello World';
	});

#### Other Basic Routes

	$app->post('foo/bar', function() {
		return 'Hello World';
	});

	$app->patch('foo/bar', function() {
		//
	});

	$app->put('foo/bar', function() {
		//
	});

	$app->delete('foo/bar', function() {
		//
	});

Often, you will need to generate URLs to your routes, you may do so using the `url` helper:

	$url = url('foo');

#### Routing Requests To Controllers

If you are interested in routing requests to classes, check out the documentation on [controllers](/docs/controllers).

<a name="route-parameters"></a>
## Route Parameters

Of course, you can capture segments of the request URI within your route:

#### Basic Route Parameter

	$app->get('user/{id}', function($id) {
		return 'User '.$id;
	});

#### Regular Expression Parameter Constraints

> **Note:** This is the only portion of Lumen that is not directly portable to the full Laravel framework. If you choose to upgrade your Lumen application to Laravel, your regular expression constraints must be moved to a `where` method call on the route.

	$app->get('user/{name:[A-Za-z]+}', function($name) {
		//
	});

<a name="named-routes"></a>
## Named Routes

Named routes allow you to conveniently generate URLs or redirects for a specific route. You may specify a name for a route with the `as` array key:

	$app->get('user/profile', ['as' => 'profile', function() {
		//
	}]);

You may also specify route names for controller actions:

	$app->get('user/profile', [
		'as' => 'profile', 'uses' => 'App\Http\Controllers\UserController@showProfile'
	]);

Now, you may use the route's name when generating URLs or redirects:

	$url = route('profile');

	$redirect = redirect()->route('profile');

<a name="route-groups"></a>
## Route Groups

Sometimes you may need to apply middleware to a group of routes. Instead of specifying the middleware on each route, you may use a route group.

Shared attributes are specified in an array format as the first parameter to the `$app->group()` method.

<a name="route-group-middleware"></a>
### Middleware

Middleware is applied to all routes within the group by defining the list of middleware with the `middleware` parameter on the group attribute array. Middleware will be executed in the order you define this array:

	$app->group(['middleware' => 'foo|bar'], function($app)
	{
		$app->get('/', function() {
			// Uses Foo & Bar Middleware
		});

		$app->get('user/profile', function() {
			// Uses Foo & Bar Middleware
		});
	});

<a name="route-group-namespace"></a>
### Namespaces

You may use the `namespace` parameter in your group attribute array to specify the namespace for all controllers within the group:

	$app->group(['namespace' => 'App\Http\Controllers\Admin'], function($app) {
		// Controllers Within The "App\Http\Controllers\Admin" Namespace
	});

<a name="route-prefixing"></a>
### Route Prefixing

A group of routes may be prefixed by using the `prefix` option in the attributes array of a group:

	$app->group(['prefix' => 'admin'], function($app)
	{
		$app->get('users', function()
		{
			// Matches The "/admin/users" URL
		});
	});

You can also utilize the `prefix` parameter to pass common parameters to your routes:

#### URL Parameter In Prefix

	$app->group(['prefix' => 'accounts/{account_id}'], function($app)
	{
		$app->get('detail', function($account_id)
		{
			//
		});
	});

<a name="csrf-protection"></a>
## CSRF Protection

> **Note:** You must [enable sessions](/docs/session#session-usage) to utilize this feature of Lumen.

Lumen, like Laravel, makes it easy to protect your application from [cross-site request forgeries](http://en.wikipedia.org/wiki/Cross-site_request_forgery). Cross-site request forgeries are a type of malicious exploit whereby unauthorized commands are performed on behalf of the authenticated user.

Lumen automatically generates a CSRF "token" for each active user session managed by the application. This token is used to verify that the authenticated user is the one actually making the requests to the application.

#### Insert The CSRF Token Into A Form

	<input type="hidden" name="_token" value="<?php echo csrf_token(); ?>">

Of course, using the Blade [templating engine](/docs/templates):

	<input type="hidden" name="_token" value="{{ csrf_token() }}">

You do not need to manually verify the CSRF token on POST, PUT, or DELETE requests. If it is enabled in the `bootstrap/app.php` file, the `Laravel\Lumen\Http\Middleware\VerifyCsrfToken` [HTTP middleware](/docs/middleware) will verify that the token in the request input matches the token stored in the session.

#### X-CSRF-TOKEN

In addition to looking for the CSRF token as a "POST" parameter, the middleware will also check for the `X-CSRF-TOKEN` request header. You could, for example, store the token in a "meta" tag and instruct jQuery to add it to all request headers:

	<meta name="csrf-token" content="{{ csrf_token() }}" />

	$.ajaxSetup({
		headers: {
			'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
		}
	});

Now all AJAX requests will automatically include the CSRF token:

	$.ajax({
	   url: "/foo/bar",
	})

#### X-XSRF-TOKEN

Lumen also stores the CSRF token in a `XSRF-TOKEN` cookie. You can use the cookie value to set the `X-XSRF-TOKEN` request header. Some Javascript frameworks, like Angular, do this automatically for you.

> Note: The difference between the `X-CSRF-TOKEN` and `X-XSRF-TOKEN` is that the first uses a plain text value and the latter uses an encrypted value, because cookies in Lumen are always encrypted when the global middleware in the `bootstrap/app.php` file are enabled.

<a name="method-spoofing"></a>
## Method Spoofing

HTML forms do not support `PUT`, `PATCH` or `DELETE` actions. So, when defining `PUT`, `PATCH` or `DELETE` routes that are called from an HTML form, you will need to add a hidden `_method` field to the form.

The value sent with the `_method` field will be used as the HTTP request method. For example:

	<form action="/foo/bar" method="POST">
		<input type="hidden" name="_method" value="PUT">
		<input type="hidden" name="_token" value="{{ csrf_token() }}">
	</form>

<a name="throwing-404-errors"></a>
## Throwing 404 Errors

There are two ways to manually trigger a 404 error from a route. First, you may use the `abort` helper:

	abort(404);

The `abort` helper simply throws a `Symfony\Component\HttpFoundation\Exception\HttpException` with the specified status code.

Secondly, you may manually throw an instance of `Symfony\Component\HttpKernel\Exception\NotFoundHttpException`.

More information on handling 404 exceptions and using custom responses for these errors may be found in the [errors](/docs/errors#http-exceptions) section of the documentation.
