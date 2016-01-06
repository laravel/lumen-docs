# Authentication

## Introduction

Authentication in Lumen, while using the same underlying libraries as Laravel, is configured quite differently from the full Laravel framework. Since Lumen does not support session state, incoming requests that you wish to authenticate must be authenticated via a stateless mechanism such as API tokens.

## Getting Started

#### Authentication Service Provider

The `AuthServiceProvider` located in your `app/Providers` directory contains a single call to `Auth::viaRequest`. The `viaRequest` method accepts a Closure which will be called when the incoming request needs to be authenticated. Within this Closure, you may resolve your `App\User` instance however you wish. If no authenticated user can be found for the request, the Closure should return `null`:

    Auth::viaRequest('api', function ($request) {
    	// Return User or null...
    });

Again, you may retrieve the authenticated user however you wish. You may use an API token in the request headers or query string, a bearer token on the request, or using any other approach your application requires.

#### Accessing The Authenticated User

Just like in the full Laravel framework, you may use the `Auth::user` method to retrieve the current user. Alternatively, you may use the `$request->user()` method on an `Illuminate\Http\Request` instance:

	use Illuminate\Http\Request;

	$app->get('/post/{id}', ['middleware' => 'auth', function (Request $request, $id) {
		$user = Auth::user();

		$user = $request->user();

		//
	}]);

> **Note:** If you would like to use `Auth::user()` to access the currently authenticated user, you should uncomment the `$app->withFacades()` method in your `bootstrap/app.php` file.

Of course, any routes you wish to authenticate should be assigned the `auth` [middleware](/docs/middleware), so you should uncomment the call to `$app->routeMiddleware()` in your `bootstrap/app.php` file:

	$app->routeMiddleware([
	    'auth' => App\Http\Middleware\Authenticate::class,
	]);
