# HTTP Controllers

- [Introduction](#introduction)
- [Basic Controllers](#basic-controllers)
- [Controller Middleware](#controller-middleware)
- [Dependency Injection & Controllers](#dependency-injection-and-controllers)

<a name="introduction"></a>
## Introduction

Instead of defining all of your request handling logic in a single `routes.php` file, you may wish to organize this behavior using Controller classes. Controllers can group related HTTP request handling logic into a class. Controllers are typically stored in the `app/Http/Controllers` directory.

<a name="basic-controllers"></a>
## Basic Controllers

Here is an example of a basic controller class:

	<?php namespace App\Http\Controllers;

	use App\User;
	use App\Http\Controllers\Controller;

	class UserController extends Controller {

		/**
		 * Show the profile for the given user.
		 *
		 * @param  int  $id
		 * @return Response
		 */
		public function showProfile($id)
		{
			return view('user.profile', ['user' => User::findOrFail($id)]);
		}

	}

We can route to the controller action like so:

	$app->get('user/{id}', 'App\Http\Controllers\UserController@showProfile');

> **Note:** All controllers should extend the base `App\Http\Controllers\Controller` class.

#### Naming Controller Routes

Like Closure routes, you may specify names on controller routes:

	$app->get('foo', ['uses' => 'App\Http\Controllers\FooController@method', 'as' => 'name']);

These names can be used to generate URLs to the controller actions:

	$url = route('name');

If the route has parameters, you may specify them like so:

	$url = route('name', ['id' => 1]);

<a name="controller-middleware"></a>
## Controller Middleware

[Middleware](/docs/middleware) may be specified on controller routes like so:

	$app->get('profile', [
		'middleware' => 'auth',
		'uses' => 'App\Http\Controllers\UserController@showProfile'
	]);

Additionally, you may specify middleware within your controller's constructor:

	class UserController extends Controller {

		/**
		 * Instantiate a new UserController instance.
		 */
		public function __construct()
		{
			$this->middleware('auth');

			$this->middleware('log', ['only' => ['fooAction', 'barAction']]);

			$this->middleware('subscribed', ['except' => ['fooAction', 'barAction']]);
		}

	}

<a name="dependency-injection-and-controllers"></a>
## Dependency Injection & Controllers

#### Constructor Injection

The Lumen / Laravel [service container](/docs/container) is used to resolve all controllers. As a result, you are able to type-hint any dependencies your controller may need in its constructor:

	<?php namespace App\Http\Controllers;

	use App\Http\Controllers\Controller;
	use App\Repositories\UserRepository;

	class UserController extends Controller {

		/**
		 * The user repository instance.
		 */
		protected $users;

		/**
		 * Create a new controller instance.
		 *
		 * @param  UserRepository  $users
		 * @return void
		 */
		public function __construct(UserRepository $users)
		{
			$this->users = $users;
		}

	}

#### Method Injection

In addition to constructor injection, you may also type-hint dependencies on your controller's methods. For example, let's type-hint the `Request` instance on one of our methods:

	<?php namespace App\Http\Controllers;

	use Illuminate\Http\Request;
	use App\Http\Controllers\Controller;

	class UserController extends Controller {

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

If your controller method is also expecting input from a route parameter, simply list your route arguments after your other dependencies:

	<?php namespace App\Http\Controllers;

	use Illuminate\Http\Request;
	use App\Http\Controllers\Controller;

	class UserController extends Controller {

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
