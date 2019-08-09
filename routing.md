# HTTP Routing

- [Basic Routing](#basic-routing)
- [Route Parameters](#route-parameters)
    - [Required Parameters](#required-parameters)
    - [Optional Parameters](#optional-parameters)
    - [Regular Expression Constraints](#parameters-regular-expression-constraints)
- [Named Routes](#named-routes)
- [Route Groups](#route-groups)
    - [Middleware](#route-group-middleware)
    - [Namespaces](#route-group-namespaces)
    - [Route Prefixes](#route-group-prefixes)

<a name="basic-routing"></a>
## Basic Routing

You will define all of the routes for your application in the `routes/web.php` file. The most basic Lumen routes simply accept a URI and a `Closure`:

    $router->get('foo', function () {
        return 'Hello World';
    });

    $router->post('foo', function () {
        //
    });

#### Available Router Methods

The router allows you to register routes that respond to any HTTP verb:

    $router->get($uri, $callback);
    $router->post($uri, $callback);
    $router->put($uri, $callback);
    $router->patch($uri, $callback);
    $router->delete($uri, $callback);
    $router->options($uri, $callback);

<a name="route-parameters"></a>
## Route Parameters

<a name="required-parameters"></a>
### Required Parameters

Of course, sometimes you will need to capture segments of the URI within your route. For example, you may need to capture a user's ID from the URL. You may do so by defining route parameters:

    $router->get('user/{id}', function ($id) {
        return 'User '.$id;
    });

You may define as many route parameters as required by your route:

    $router->get('posts/{postId}/comments/{commentId}', function ($postId, $commentId) {
        //
    });

Route parameters are always encased within "curly" braces. The parameters will be passed into your route's `Closure` when the route is executed.

> **Note:** Route parameters cannot contain the `-` character. Use an underscore (`_`) instead.

<a name="optional-parameters"></a>
### Optional Parameters

You may define optional route parameters by enclosing part of the route URI definition in `[...]`. So, for example, `/foo[bar]` will match both `/foo` and `/foobar`. Optional parameters are only supported in a trailing position of the URI. In other words, you may not place an optional parameter in the middle of a route definition:

    $router->get('user[/{name}]', function ($name = null) {
        return $name;
    });

<a name="parameters-regular-expression-constraints"></a>
### Regular Expression Constraints

You may constrain the format of your route parameters by defining a regular expression in your route definition:

    $router->get('user/{name:[A-Za-z]+}', function ($name) {
        //
    });

<a name="named-routes"></a>
## Named Routes

Named routes allow the convenient generation of URLs or redirects for specific routes. You may specify a name for a route using the `as` array key when defining the route:

    $router->get('profile', ['as' => 'profile', function () {
        //
    }]);

You may also specify route names for controller actions:

    $router->get('profile', [
        'as' => 'profile', 'uses' => 'UserController@showProfile'
    ]);

#### Generating URLs To Named Routes

Once you have assigned a name to a given route, you may use the route's name when generating URLs or redirects via the global `route` function:

    // Generating URLs...
    $url = route('profile');

    // Generating Redirects...
    return redirect()->route('profile');

If the named route defines parameters, you may pass the parameters as the second argument to the `route` function. The given parameters will automatically be inserted into the URL in their correct positions:

    $router->get('user/{id}/profile', ['as' => 'profile', function ($id) {
        //
    }]);

    $url = route('profile', ['id' => 1]);

<a name="route-groups"></a>
## Route Groups

Route groups allow you to share route attributes, such as middleware or namespaces, across a large number of routes without needing to define those attributes on each individual route. Shared attributes are specified in an array format as the first parameter to the `$router->group` method.

To learn more about route groups, we'll walk through several common use-cases for the feature.

<a name="route-group-middleware"></a>
### Middleware

To assign middleware to all routes within a group, you may use the `middleware` key in the group attribute array. Middleware will be executed in the order you define this array:

    $router->group(['middleware' => 'auth'], function () use ($router) {
        $router->get('/', function () {
            // Uses Auth Middleware
        });

        $router->get('user/profile', function () {
            // Uses Auth Middleware
        });
    });

<a name="route-group-namespaces"></a>
### Namespaces

Another common use-case for route groups is assigning the same PHP namespace to a group of controllers. You may use the `namespace` parameter in your group attribute array to specify the namespace for all controllers within the group:

    $router->group(['namespace' => 'Admin'], function() use ($router)
    {
        // Using The "App\Http\Controllers\Admin" Namespace...

        $router->group(['namespace' => 'User'], function() use ($router) {
            // Using The "App\Http\Controllers\Admin\User" Namespace...
        });
    });

<a name="route-group-prefixes"></a>
### Route Prefixes

The `prefix` group attribute may be used to prefix each route in the group with a given URI. For example, you may want to prefix all route URIs within the group with `admin`:

    $router->group(['prefix' => 'admin'], function () use ($router) {
        $router->get('users', function () {
            // Matches The "/admin/users" URL
        });
    });

You may also use the `prefix` parameter to specify common parameters for your grouped routes:

    $router->group(['prefix' => 'accounts/{accountId}'], function () use ($router) {
        $router->get('detail', function ($accountId) {
            // Matches The "/accounts/{accountId}/detail" URL
        });
    });
