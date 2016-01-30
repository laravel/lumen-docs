# HTTP Routing

- [Basic Routing](#basic-routing)
- [Route Parameters](#route-parameters)
	- [Required Parameters](#required-parameters)
	- [Regular Expression Constraints](#parameters-regular-expression-constraints)
- [Named Routes](#named-routes)
- [Route Groups](#route-groups)
	- [Middleware](#route-group-middleware)
	- [Namespaces](#route-group-namespaces)
	- [Route Prefixes](#route-group-prefixes)
- [CSRF Protection](#csrf-protection)
	- [Introduction](#csrf-introduction)
	- [X-CSRF-Token](#csrf-x-csrf-token)
	- [X-XSRF-Token](#csrf-x-xsrf-token)
- [Form Method Spoofing](#form-method-spoofing)
- [Throwing 404 Errors](#throwing-404-errors)

<a name="basic-routing"></a>
## Basic Routing

You will define most of the routes for your application in the `app/Http/routes.php` file, which is loaded by the `bootstrap/app.php` file. The most basic Lumen routes simply accept a URI and a `Closure`:

	$app->get('/', function () {
		return 'Hello World';
	});

	$app->post('foo/bar', function () {
		return 'Hello World';
	});

	$app->put('foo/bar', function () {
		//
	});

	$app->delete('foo/bar', function () {
		//
	});

#### Generating URLs To Routes

You may generate URLs to your application's routes using the `url` helper:

	$url = url('foo');

<a name="route-parameters"></a>
## Route Parameters

<a name="required-parameters"></a>
### Required Parameters

Of course, sometimes you will need to capture segments of the URI within your route. For example, you may need to capture a user's ID from the URL. You may do so by defining route parameters:

	$app->get('user/{id}', function ($id) {
		return 'User '.$id;
	});

You may define as many route parameters as required by your route:

	$app->get('posts/{post}/comments/{comment}', function ($postId, $commentId) {
		//
	});

Route parameters are always encased within "curly" braces. The parameters will be passed into your route's `Closure` when the route is executed.

> **Note:** Route parameters cannot contain the `-` character. Use an underscore (`_`) instead.

<a name="parameters-regular-expression-constraints"></a>
### Regular Expression Constraints

You may constrain the format of your route parameters by defining a regular expression in your route definition:

	$app->get('user/{name:[A-Za-z]+}', function ($name) {
		//
	});

<a name="named-routes"></a>
## Named Routes

Named routes allow you to conveniently generate URLs or redirects for a specific route. You may specify a name for a route using the `as` array key when defining the route:

	$app->get('user/profile', ['as' => 'profile', function () {
		//
	}]);

You may also specify route names for controller actions:

	$app->get('user/profile', [
		'as' => 'profile', 'uses' => 'App\Http\Controllers\UserController@showProfile'
	]);

#### Generating URLs To Named Routes

Once you have assigned a name to a given route, you may use the route's name when generating URLs or redirects via the `route` function:

	$url = route('profile');

	$redirect = redirect()->route('profile');

If the route defines parameters, you may pass the parameters as the second argument to the `route` method. The given parameters will automatically be inserted into the URL:

	$app->get('user/{id}/profile', ['as' => 'profile', function ($id) {
		//
	}]);

	$url = route('profile', ['id' => 1]);

<a name="route-groups"></a>
## Route Groups

Route groups allow you to share route attributes, such as middleware or namespaces, across a large number of routes without needing to define those attributes on each individual routes. Shared attributes are specified in an array format as the first parameter to the `$app->group` method.

To learn more about route groups, we'll walk through several common use-cases for the feature.

<a name="route-group-middleware"></a>
### Middleware

To assign middleware to all routes within a group, you may use the `middleware` key in the group attribute array. Middleware will be executed in the order you define this array:

	$app->group(['middleware' => 'auth'], function ($app) {
		$app->get('/', function ()	{
			// Uses Auth Middleware
		});

		$app->get('user/profile', function () {
			// Uses Auth Middleware
		});
	});

<a name="route-group-namespaces"></a>
### Namespaces

Another common use-case for route groups is assigning the same PHP namespace to a group of controllers. You may use the `namespace` parameter in your group attribute array to specify the namespace for all controllers within the group:

	$app->group(['namespace' => 'App\Http\Controllers\Admin'], function ($app) {

		// Controllers Within The "App\Http\Controllers\Admin" Namespace

	});

<a name="route-group-prefixes"></a>
### Route Prefixes

The `prefix` group array attribute may be used to prefix each route in the group with a given URI. For example, you may want to prefix all route URIs within the group with `admin`:

	$app->group(['prefix' => 'admin'], function ($app) {
		$app->get('users', function ()	{
			// Matches The "/admin/users" URL
		});
	});

You may also use the `prefix` parameter to specify common parameters for your grouped routes:

	$app->group(['prefix' => 'accounts/{account_id}'], function ($app) {
		$app->get('detail', function ($account_id)	{
			// Matches The accounts/{account_id}/detail URL
		});
	});

<a name="csrf-protection"></a>
## CSRF Protection

> **Note:** You must [enable sessions](/docs/{{version}}/session) before using this Lumen feature.

<a name="csrf-introduction"></a>
### Introduction

Lumen makes it easy to protect your application from [cross-site request forgeries](http://en.wikipedia.org/wiki/Cross-site_request_forgery). Cross-site request forgeries are a type of malicious exploit whereby unauthorized commands are performed on behalf of the authenticated user.

Lumen automatically generates a CSRF "token" for each active user session managed by the application. This token is used to verify that the authenticated user is the one actually making the requests to the application. To retrieve the current CSRF token value, use the `csrf_token` helper:

	<?php echo csrf_token(); ?>

	<input type="hidden" name="_token" value="<?php echo csrf_token(); ?>">

You do not need to manually verify the CSRF token on POST, PUT, or DELETE requests. The `VerifyCsrfToken` [HTTP middleware](/docs/{{version}}/middleware) will verify token in the request input matches the token stored in the session.

<a name="csrf-x-csrf-token"></a>
### X-CSRF-TOKEN

In addition to checking for the CSRF token as a POST parameter, the Lumen `VerifyCsrfToken` middleware will also check for the `X-CSRF-TOKEN` request header. You could, for example, store the token in a "meta" tag:

	<meta name="csrf-token" content="{{ csrf_token() }}">

Once you have created the `meta` tag, you can instruct a library like jQuery to add the token to all request headers. This provides simple, convenient CSRF protection for your AJAX based applications:

	$.ajaxSetup({
			headers: {
				'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
			}
	});

<a name="csrf-x-xsrf-token"></a>
### X-XSRF-TOKEN

Lumen also stores the CSRF token in a `XSRF-TOKEN` cookie. You can use the cookie value to set the `X-XSRF-TOKEN` request header. Some JavaScript frameworks, like Angular, do this automatically for you. It is unlikely that you will need to use this value manually.

<a name="form-method-spoofing"></a>
## Form Method Spoofing

HTML forms do not support `PUT`, `PATCH` or `DELETE` actions. So, when defining `PUT`, `PATCH` or `DELETE` routes that are called from an HTML form, you will need to add a hidden `_method` field to the form. The value sent with the `_method` field will be used as the HTTP request method:

	<form action="/foo/bar" method="POST">
		<input type="hidden" name="_method" value="PUT">
		<input type="hidden" name="_token" value="{{ csrf_token() }}">
	</form>

<a name="throwing-404-errors"></a>
## Throwing 404 Errors

There are two ways to manually trigger a 404 error from a route. First, you may use the `abort` helper. The `abort` helper simply throws a `Symfony\Component\HttpFoundation\Exception\HttpException` with the specified status code:

	abort(404);

Secondly, you may manually throw an instance of `Symfony\Component\HttpKernel\Exception\NotFoundHttpException`.

More information on handling 404 exceptions and using custom responses for these errors may be found in the [errors](/docs/{{version}}/errors#http-exceptions) section of the documentation.
