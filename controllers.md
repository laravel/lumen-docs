# HTTP Controllers

- [Introduction](#introduction)
- [Basic Controllers](#basic-controllers)
- [Controller Middleware](#controller-middleware)
- [Dependency Injection & Controllers](#dependency-injection-and-controllers)

<a name="introduction"></a>
## Introduction

Instead of defining all of your request handling logic in a single `routes.php` file, you may wish to organize this behavior using Controller classes. Controllers can group related HTTP request handling logic into a class. Controllers are stored in the `app/Http/Controllers` directory.

<a name="basic-controllers"></a>
## Basic Controllers

Here is an example of a basic controller class. All Lumen controllers should extend the base controller class included with the default Lumen installation:

    <?php

    namespace App\Http\Controllers;

    use App\User;

    class UserController extends Controller
    {
        /**
         * Retrieve the user for the given ID.
         *
         * @param  int  $id
         * @return Response
         */
        public function show($id)
        {
            return User::findOrFail($id);
        }
    }

We can route to the controller action like so:

    $app->get('user/{id}', 'UserController@show');

Now, when a request matches the specified route URI, the `show` method on the `UserController` class will be executed. Of course, the route parameters will also be passed to the method.

#### Controllers & Namespaces

It is very important to note that we did not need to specify the full controller namespace when defining the controller route. We only defined the portion of the class name that comes after the `App\Http\Controllers` namespace "root". By default, the `bootstrap/app.php` file will load the `routes.php` file within a route group containing the root controller namespace.

If you choose to nest or organize your controllers using PHP namespaces deeper into the `App\Http\Controllers` directory, simply use the specific class name relative to the `App\Http\Controllers` root namespace. So, if your full controller class is `App\Http\Controllers\Photos\AdminController`, you would register a route like so:

    $app->get('foo', 'Photos\AdminController@method');

#### Naming Controller Routes

Like Closure routes, you may specify names on controller routes:

    $app->get('foo', ['uses' => 'FooController@method', 'as' => 'name']);

You may also use the `route` helper to generate a URL to a named controller route:

    $url = route('name');

<a name="controller-middleware"></a>
## Controller Middleware

[Middleware](/docs/{{version}}/middleware) may be assigned to the controller's routes like so:

    $app->get('profile', [
        'middleware' => 'auth',
        'uses' => 'UserController@showProfile'
    ]);

However, it is more convenient to specify middleware within your controller's constructor. Using the `middleware` method from your controller's constructor, you may easily assign middleware to the controller. You may even restrict the middleware to only certain methods on the controller class:

    class UserController extends Controller
    {
        /**
         * Instantiate a new UserController instance.
         *
         * @return void
         */
        public function __construct()
        {
            $this->middleware('auth');

            $this->middleware('log', ['only' => [
                'fooAction',
                'barAction',
            ]]);

            $this->middleware('subscribed', ['except' => [
                'fooAction',
                'barAction',
            ]]);
        }
    }

<a name="dependency-injection-and-controllers"></a>
## Dependency Injection & Controllers

#### Constructor Injection

The Lumen [service container](/docs/{{version}}/container) is used to resolve all Lumen controllers. As a result, you are able to type-hint any dependencies your controller may need in its constructor. The dependencies will automatically be resolved and injected into the controller instance:

    <?php

    namespace App\Http\Controllers;

    use App\Repositories\UserRepository;

    class UserController extends Controller
    {
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

In addition to constructor injection, you may also type-hint dependencies on your controller's action methods. For example, let's type-hint the `Illuminate\Http\Request` instance on one of our methods:

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
    use Illuminate\Routing\Controller;

    class UserController extends Controller
    {
        /**
         * Update the specified user.
         *
         * @param  Request  $request
         * @param  string  $id
         * @return Response
         */
        public function update(Request $request, $id)
        {
            //
        }
    }
