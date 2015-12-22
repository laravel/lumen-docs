# HTTP Routing

- [Basic Routing](#basic-routing)
- [Route Parameters](#route-parameters)
    - [Required Parameters](#required-parameters)
- [Named Routes](#named-routes)
- [Route Groups](#route-groups)
    - [Middleware](#route-group-middleware)
    - [Namespaces](#route-group-namespaces)
    - [Route Prefixes](#route-group-prefixes)
- [CSRF Protection](#csrf-protection)
    - [Introduction](#csrf-introduction)
    - [Excluding URIs](#csrf-excluding-uris)
    - [X-CSRF-Token](#csrf-x-csrf-token)
    - [X-XSRF-Token](#csrf-x-xsrf-token)
- [Route Model Binding](#route-model-binding)
- [Form Method Spoofing](#form-method-spoofing)

<a name="basic-routing"></a>
## Basic Routing

You will define all of the routes for your application in the `app/Http/routes.php` file. The most basic Lumen routes simply accept a URI and a `Closure`:

    $app->get('foo', function () {
        return 'Hello World';
    });

    $app->post('foo', function () {
        //
    });

#### Available Router Methods

The router allows you to register routes that respond to any HTTP verb:

    $app->get($uri, $callback);
    $app->post($uri, $callback);
    $app->put($uri, $callback);
    $app->patch($uri, $callback);
    $app->delete($uri, $callback);
    $app->options($uri, $callback);

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

<a name="named-routes"></a>
## Named Routes

Named routes allow the convenient generation of URLs or redirects for specific routes. You may specify a name for a route using the `as` array key when defining the route:

    $app->get('profile', ['as' => 'profile', function () {
        //
    }]);

You may also specify route names for controller actions:

    $app->get('profile', [
        'as' => 'profile', 'uses' => 'UserController@showProfile'
    ]);

#### Generating URLs To Named Routes

Once you have assigned a name to a given route, you may use the route's name when generating URLs or redirects via the global `route` function:

    // Generating URLs...
    $url = route('profile');

    // Generating Redirects...
    return redirect()->route('profile');

If the named route defines parameters, you may pass the parameters as the second argument to the `route` function. The given parameters will automatically be inserted into the URL in their correct positions:

    $app->get('user/{id}/profile', ['as' => 'profile', function ($id) {
        //
    }]);

    $url = route('profile', ['id' => 1]);

<a name="route-groups"></a>
## Route Groups

Route groups allow you to share route attributes, such as middleware or namespaces, across a large number of routes without needing to define those attributes on each individual route. Shared attributes are specified in an array format as the first parameter to the `$app->group` method.

To learn more about route groups, we'll walk through several common use-cases for the feature.

<a name="route-group-middleware"></a>
### Middleware

To assign middleware to all routes within a group, you may use the `middleware` key in the group attribute array. Middleware will be executed in the order you define this array:

    $app->group(['middleware' => 'auth'], function () {
        $app->get('/', function ()    {
            // Uses Auth Middleware
        });

        $app->get('user/profile', function () {
            // Uses Auth Middleware
        });
    });

<a name="route-group-namespaces"></a>
### Namespaces

Another common use-case for route groups is assigning the same PHP namespace to a group of controllers. You may use the `namespace` parameter in your group attribute array to specify the namespace for all controllers within the group:

    $app->group(['namespace' => 'Admin'], function()
    {
        // Controllers Within The "App\Http\Controllers\Admin" Namespace

        $app->group(['namespace' => 'User'], function() {
            // Controllers Within The "App\Http\Controllers\Admin\User" Namespace
        });
    });

Remember, by default, the `bootstrap/app.php` file includes your `routes.php` file within a namespace group, allowing you to register controller routes without specifying the full `App\Http\Controllers` namespace prefix. So, we only need to specify the portion of the namespace that comes after the base `App\Http\Controllers` namespace.

<a name="route-group-prefixes"></a>
### Route Prefixes

The `prefix` group attribute may be used to prefix each route in the group with a given URI. For example, you may want to prefix all route URIs within the group with `admin`:

    $app->group(['prefix' => 'admin'], function () {
        $app->get('users', function ()    {
            // Matches The "/admin/users" URL
        });
    });

You may also use the `prefix` parameter to specify common parameters for your grouped routes:

    $app->group(['prefix' => 'accounts/{account_id}'], function () {
        $app->get('detail', function ($accountId)    {
            // Matches The "/accounts/{account_id}/detail" URL
        });
    });

<a name="csrf-protection"></a>
## CSRF Protection

<a name="csrf-introduction"></a>
### Introduction

Lumen makes it easy to protect your application from [cross-site request forgery](http://en.wikipedia.org/wiki/Cross-site_request_forgery) (CSRF) attacks. Cross-site request forgeries are a type of malicious exploit whereby unauthorized commands are performed on behalf of an authenticated user.

Lumen automatically generates a CSRF "token" for each active user session managed by the application. This token is used to verify that the authenticated user is the one actually making the requests to the application.

Anytime you define a HTML form in your application, you should include a hidden CSRF token field in the form so that the CSRF protection middleware will be able to validate the request. To generate a hidden input field `_token` containing the CSRF token, you may use the `csrf_field` helper function:

    // Vanilla PHP
    <?php echo csrf_field(); ?>

    // Blade Template Syntax
    {{ csrf_field() }}

The `csrf_field` helper function generates the following HTML:

    <input type="hidden" name="_token" value="<?php echo csrf_token(); ?>">

You do not need to manually verify the CSRF token on POST, PUT, or DELETE requests. The `VerifyCsrfToken` [middleware](/docs/{{version}}/middleware), which is included in the `web` middleware group, will automatically verify that the token in the request input matches the token stored in the session.

<a name="csrf-excluding-uris"></a>
### Excluding URIs From CSRF Protection

Sometimes you may wish to exclude a set of URIs from CSRF protection. For example, if you are using [Stripe](https://stripe.com) to process payments and are utilizing their webhook system, you will need to exclude your webhook handler route from Lumen's CSRF protection.

You may exclude URIs by defining their routes outside of the `web` middleware group that is included in the default `routes.php` file, or by adding the URIs to the `$except` property of the `VerifyCsrfToken` middleware:

    <?php

    namespace App\Http\Middleware;

    use Illuminate\Foundation\Http\Middleware\VerifyCsrfToken as BaseVerifier;

    class VerifyCsrfToken extends BaseVerifier
    {
        /**
         * The URIs that should be excluded from CSRF verification.
         *
         * @var array
         */
        protected $except = [
            'stripe/*',
        ];
    }

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

<a name="route-model-binding"></a>
## Route Model Binding

Lumen route model binding provides a convenient way to inject model instances into your routes. For example, instead of injecting a user's ID, you can inject the entire `User` model instance that matches the given ID.

### Implicit Binding

Lumen will automatically resolve type-hinted Eloquent model's defined in routes or controller actions whose variable names match a route segment name. For example:

    $app->get('api/users/{user}', function (App\User $user) {
        return $user->email;
    });

In this example, since the Eloquent type-hinted `$user` variable defined on the route matches the `{user}` segment in the route's URI, Lumen will automatically inject the model instance that has an ID matching the corresponding value from the request URI.

If a matching model instance is not found in the database, a 404 HTTP response will be automatically generated.

#### Customizing The Key Name

If you would like the implicit model binding to use a database column other than `id` when retrieving models, you may override the `getRouteKeyName` method on your Eloquent model:

    /**
     * Get the route key for the model.
     *
     * @return string
     */
    public function getRouteKeyName()
    {
        return 'slug';
    }

### Explicit Binding

To register an explicit binding, use the router's `model` method to specify the class for a given parameter. You should define your model bindings in the `RouteServiceProvider::boot` method:

#### Binding A Parameter To A Model

    public function boot(Router $router)
    {
        parent::boot($router);

        $router->model('user', 'App\User');
    }

Next, define a route that contains a `{user}` parameter:

    $router->get('profile/{user}', function(App\User $user) {
        //
    });

Since we have bound the `{user}` parameter to the `App\User` model, a `User` instance will be injected into the route. So, for example, a request to `profile/1` will inject the `User` instance which has an ID of 1.

If a matching model instance is not found in the database, a 404 HTTP response will be automatically generated.

#### Customizing The Resolution Logic

If you wish to use your own resolution logic, you should use the `$app->bind` method. The Closure you pass to the `bind` method will receive the value of the URI segment, and should return an instance of the class you want to be injected into the route:

    $router->bind('user', function($value) {
        return App\User::where('name', $value)->first();
    });

#### Customizing The "Not Found" Behavior

If you wish to specify your own "not found" behavior, pass a Closure as the third argument to the `model` method:

    $router->model('user', 'App\User', function() {
        throw new NotFoundHttpException;
    });

<a name="form-method-spoofing"></a>
## Form Method Spoofing

HTML forms do not support `PUT`, `PATCH` or `DELETE` actions. So, when defining `PUT`, `PATCH` or `DELETE` routes that are called from an HTML form, you will need to add a hidden `_method` field to the form. The value sent with the `_method` field will be used as the HTTP request method:

    <form action="/foo/bar" method="POST">
        <input type="hidden" name="_method" value="PUT">
        <input type="hidden" name="_token" value="{{ csrf_token() }}">
    </form>

To generate the hidden input field `_method`, you may also use the `method_field` helper function:

    <?php echo method_field('PUT'); ?>

Of course, using the Blade [templating engine](/docs/{{version}}/blade):

    {{ method_field('PUT') }}
