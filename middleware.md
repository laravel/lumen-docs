# HTTP Middleware

- [Introduction](#introduction)
- [Defining Middleware](#defining-middleware)
- [Registering Middleware](#registering-middleware)
- [Middleware Parameters](#middleware-parameters)
- [Terminable Middleware](#terminable-middleware)

<a name="introduction"></a>
## Introduction

HTTP middleware provide a convenient mechanism for filtering HTTP requests entering your application. For example, Lumen includes a middleware that verifies the user of your application is authenticated. If the user is not authenticated, the middleware will redirect the user to the login screen. However, if the user is authenticated, the middleware will allow the request to proceed further into the application.

Of course, additional middleware can be written to perform a variety of tasks besides authentication. A CORS middleware might be responsible for adding the proper headers to all responses leaving your application. A logging middleware might log all incoming requests to your application.

<a name="defining-middleware"></a>
## Defining Middleware

Middleware are typically placed in the `app/Http/Middleware` directory. To create a new middleware, define a class with a `handle` method like the following:

    /**
     * Filter the incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        //
    }

For example, we may define a middleware to only allow access to the route if the supplied `age` is greater than 200. Otherwise, we will redirect the users back to the "home" URI:

	<?php

	namespace App\Http\Middleware;

	use Closure;

	class OldMiddleware
	{
		/**
		 * Filter the incoming request.
		 *
		 * @param  \Illuminate\Http\Request  $request
		 * @param  \Closure  $next
		 * @return mixed
		 */
		public function handle($request, Closure $next)
		{
			if ($request->input('age') <= 200) {
				return redirect('home');
			}

			return $next($request);
		}

	}

As you can see, if the given `age` is less than or equal to `200`, the middleware will return an HTTP redirect to the client; otherwise, the request will be passed further into the application. To pass the request deeper into the application (allowing the middleware to "pass"), simply call the `$next` callback with the `$request`.

It's best to envision middleware as a series of "layers" HTTP requests must pass through before they hit your application. Each layer can examine the request and even reject it entirely.

### *Before* / *After* Middleware

Whether a middleware runs before or after a request depends on the middleware itself. For example, the following middleware would perform some task **before** the request is handled by the application:

	<?php

	namespace App\Http\Middleware;

	use Closure;

	class BeforeMiddleware
	{
		public function handle($request, Closure $next)
		{
			// Perform action

			return $next($request);
		}
	}

However, this middleware would perform its task **after** the request is handled by the application:

	<?php

	namespace App\Http\Middleware;

	use Closure;

	class AfterMiddleware
	{
		public function handle($request, Closure $next)
		{
			$response = $next($request);

			// Perform action

			return $response;
		}
	}

<a name="registering-middleware"></a>
## Registering Middleware

### Global Middleware

If you want a middleware to be run during every HTTP request to your application, simply list the middleware class in the `$app->middleware()` call in your `bootstrap/app.php` file.

### Assigning Middleware To Routes

If you would like to assign middleware to specific routes, you should first assign the middleware a short-hand key in your `bootstrap/app.php` file. By default, the `$app->routeMiddleware()` method call of this file contains entries for the middleware included with Lumen. To add your own, simply append it to this list and assign it a key of your choosing. For example:

	$app->routeMiddleware([
	    'old' => 'App\Http\Middleware\OldMiddleware',
	]);

Once the middleware has been defined in the bootstrap file, you may use the `middleware` key in the route options array:

	$app->get('admin/profile', ['middleware' => 'auth', function () {
		//
	}]);

<a name="middleware-parameters"></a>
## Middleware Parameters

Middleware can also receive additional custom parameters. For example, if your application needs to verify that the authenticated user has a given "role" before performing a given action, you could create a `RoleMiddleware` that receives a role name as an additional argument.

Additional middleware parameters will be passed to the middleware after the `$next` argument:

	<?php

	namespace App\Http\Middleware;

	use Closure;

	class RoleMiddleware
	{
		/**
		 * Run the request filter.
		 *
		 * @param  \Illuminate\Http\Request  $request
		 * @param  \Closure  $next
		 * @param  string  $role
		 * @return mixed
		 */
		public function handle($request, Closure $next, $role)
		{
			if (! $request->user()->hasRole($role)) {
				// Redirect...
			}

			return $next($request);
		}

	}

Middleware parameters may be specified when defining the route by separating the middleware name and parameters with a `:`. Multiple parameters should be delimited by commas:

	$app->put('post/{id}', ['middleware' => 'role:editor', function ($id) {
		//
	}]);

<a name="terminable-middleware"></a>
## Terminable Middleware

Sometimes a middleware may need to do some work after the HTTP response has already been sent to the browser. For example, the "session" middleware included with Lumen writes the session data to storage _after_ the response has been sent to the browser. To accomplish this, define the middleware as "terminable" by adding a `terminate` method to the middleware:

	<?php namespace Illuminate\Session\Middleware;

	use Closure;

	class StartSession
	{
		public function handle($request, Closure $next)
		{
			return $next($request);
		}

		public function terminate($request, $response)
		{
			// Store the session data...
		}
	}

The `terminate` method should receive both the request and the response. Once you have defined a terminable middleware, you should add it to the list of global middlewares in your bootstrap file.
