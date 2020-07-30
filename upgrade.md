# Upgrade Guide

- [Upgrading To 7.0.0 From 6.x](#upgrade-6.x)
- [Upgrading To 6.0.0 From 5.8](#upgrade-6.0.0)
- [Upgrading To 5.8.0 From 5.7](#upgrade-5.8.0)
- [Upgrading To 5.7.0 From 5.6](#upgrade-5.7.0)
- [Upgrading To 5.6.0 From 5.5](#upgrade-5.6.0)
- [Upgrading To 5.5.0 From 5.4](#upgrade-5.5.0)
- [Upgrading To 5.4.0 From 5.3](#upgrade-5.4.0)
- [Upgrading To 5.3.0 From 5.2](#upgrade-5.3.0)
- [Upgrading To 5.2.0 From 5.1](#upgrade-5.2.0)

<a name="upgrade-6.x"></a>
## Upgrading To 7.0.0 From 6.x

Lumen 7.0 serves as a maintenance release to upgrade the underlying Laravel packages to the 7.x release series. Before upgrading your application to Lumen 7.0, you should review the Laravel 7.0 [upgrade guide](https://laravel.com/docs/7.x/upgrade) and make any applicable changes to your application according to which Laravel components you are using.

Once you have made the necessary adjustments to your application, you may upgrade your Lumen framework dependency in your `composer.json` file and run the `composer update` command:

    "laravel/lumen-framework": "^7.0"

### Timezone Setting

The timezone setting has been moved from the `lumen-framework` package to the `bootstrap/app.php` file.  
If you're currently using the `APP_TIMEZONE` environment variable you should add the following to your `bootstrap/app.php` file:

    date_default_timezone_set(env('APP_TIMEZONE', 'UTC'));

### Symfony 5 Related Upgrades

Lumen 7 utilizes the 5.x series of the Symfony components. Some minor changes to your application are required to accommodate this upgrade.

First, the `report` and `render` methods of your application's `App\Exceptions\Handler` class should accept instances of the `Throwable` interface instead of `Exception` instances:

    use Throwable;

    public function report(Throwable $exception);
    public function render($request, Throwable $exception);

<a name="upgrade-6.0.0"></a>
## Upgrading To 6.0.0 From 5.8

Lumen 6.0 serves as a maintenance release to upgrade the underlying Laravel packages to the 6.0 release series. Before upgrading your application to Lumen 6.0, you should review the Laravel 6.0 [upgrade guide](https://laravel.com/docs/6.0/upgrade#upgrade-6.0) and make any applicable changes to your application according to which Laravel components you are using.

Once you have made the necessary adjustments to your application, you may upgrade your Lumen framework dependency in your `composer.json` file and run the `composer update` command:

    "laravel/lumen-framework": "^6.0"

<a name="upgrade-5.8.0"></a>
## Upgrading To 5.8.0 From 5.7

Lumen 5.8 serves as a maintenance release to upgrade the underlying Laravel packages to the 5.8 release series. Before upgrading your application to Lumen 5.8, you should review the Laravel 5.8 [upgrade guide](https://laravel.com/docs/5.8/upgrade#upgrade-5.8.0) and make any applicable changes to your application according to which Laravel components you are using.

Once you have made the necessary adjustments to your application, you may upgrade your Lumen framework dependency in your `composer.json` file and run the `composer update` command:

    "laravel/lumen-framework": "5.8.*"

### Environment Variables

Lumen 5.8 makes use of the newest implementation of the `vlucas/phpdotenv` library. You'll need to change the code which loads the environment variables in your `bootstrap/app.php` file at the top to the following:

	(new Laravel\Lumen\Bootstrap\LoadEnvironmentVariables(
		dirname(__DIR__)
	))->bootstrap();

Additionally, you'll need to update your dependency in your `composer.json` to the new version:

	"vlucas/phpdotenv": "^3.3"

<a name="upgrade-5.7.0"></a>
## Upgrading To 5.7.0 From 5.6

Lumen 5.7 serves as a maintenance release to upgrade the underlying Laravel packages to the 5.7 release series. Before upgrading your application to Lumen 5.7, you should review the Laravel 5.7 [upgrade guide](https://laravel.com/docs/5.7/upgrade#upgrade-5.7.0) and make any applicable changes to your application according to which Laravel components you are using.

Once you have made the necessary adjustments to your application, you may upgrade your Lumen framework dependency in your `composer.json` file and run the `composer update` command:

    "laravel/lumen-framework": "5.7.*"

<a name="upgrade-5.6.0"></a>
## Upgrading To 5.6.0 From 5.5

Lumen 5.6 serves as a maintenance release to upgrade the underlying Laravel packages to the 5.6 release series. Before upgrading your application to Lumen 5.6, you should review the Laravel 5.6 [upgrade guide](https://laravel.com/docs/5.6/upgrade#upgrade-5.6.0) and make any applicable changes to your application according to which Laravel components you are using.

Once you have made the necessary adjustments to your application, you may upgrade your Lumen framework dependency in your `composer.json` file and run the `composer update` command:

    "laravel/lumen-framework": "5.6.*"

<a name="upgrade-5.5.0"></a>
## Upgrading To 5.5.0 From 5.4

Lumen 5.5 serves as a maintenance release to upgrade the underlying Laravel packages to the 5.5 release series. Before upgrading your application to Lumen 5.5, you should review the Laravel 5.5 [upgrade guide](https://laravel.com/docs/5.5/upgrade#upgrade-5.5.0) and make any applicable changes to your application according to which Laravel components you are using.

Once you have made the necessary adjustments to your application, you may upgrade your Lumen framework dependency in your `composer.json` file and run the `composer update` command:

    "laravel/lumen-framework": "5.5.*"

### Updating The Bootstrap File

In Laravel 5.5, the service container implements the PSR-11 interface, causing conflicts with the `get` method of the router when attempting to use the service container as a router proxy. For this reason, the `bootstrap/app.php` file needs to be updated to pass only a `Router` instance to the `routes.php` file. Near the bottom of your `bootstrap/app.php` file, adjust the inclusion of the `routes.php` file to look like the following:

    $app->router->group([
        'namespace' => 'App\Http\Controllers',
    ], function ($router) {
        require __DIR__.'/../routes/web.php';
    });

### Updating The Routes File

After updating your `bootstrap/app.php` file, you should update your `routes/web.php` file to use the `$router` variable instead of the `$app` variable:

    $router->get('/hello', function () {
        return 'Hello World';
    });

<a name="upgrade-5.4.0"></a>
## Upgrading To 5.4.0 From 5.3

Lumen 5.4 serves as a maintenance release to upgrade the underlying Laravel packages to the 5.4 release series. Before upgrading your application to Lumen 5.4, you should review the Laravel 5.4 [upgrade guide](https://laravel.com/docs/5.4/upgrade#upgrade-5.4.0) and make any applicable changes to your application according to which Laravel components you are using.

Once you have made the necessary adjustments to your application, you may upgrade your Lumen framework dependency in your `composer.json` file and run the `composer update` command:

    "laravel/lumen-framework": "5.4.*"

#### Requests In Service Providers

If you were previously accessing the current request in one of your application's service providers, you should refactor your code to use a middleware instead.

<a name="upgrade-5.3.0"></a>
## Upgrading To 5.3.0 From 5.2

Lumen 5.3 does not change the structure of the framework. Instead, it serves as a maintenance release to upgrade the underlying Laravel packages to the 5.3 release series. Before upgrading your application to Lumen 5.3, you should review the Laravel 5.3 [upgrade guide](https://laravel.com/docs/5.3/upgrade#upgrade-5.3.0) and make any applicable changes to your application according to which Laravel components you are using.

Once you have made the necessary adjustments to your application, you may upgrade your Lumen framework dependency in your `composer.json` file and run the `composer update` command:

    "laravel/lumen-framework": "5.3.*"

<a name="upgrade-5.2.0"></a>
## Upgrading To 5.2.0 From 5.1

Lumen 5.2 represents a more decided shift towards focusing on stateless APIs. Therefore, sessions have been removed from the framework. If you would like to use these features, you should upgrade your Lumen 5.1 application to Laravel 5.2.

Upgrading your Lumen application to the full Laravel framework mainly involves copying your routes and classes over into a fresh installation of Laravel. Since Laravel and Lumen share many of the same components, your classes should not require any modification.

### Updating Dependencies

Update your `composer.json` file to point to `laravel/lumen-framework 5.2.*` and `vlucas/phpdotenv ~2.2`.

### Bootstrap

In the `bootstrap/app.php` file you need to modify the `Dotenv::load(...)` method call to the following:

    try {
        (new Dotenv\Dotenv(__DIR__.'/../'))->load();
    } catch (Dotenv\Exception\InvalidPathException $e) {
        //
    }

### Application

Lumen no longer implements the `Illuminate\Contracts\Foundation\Application` contract.  Any `Application` contract type-hints should be updated to reference the `Laravel\Lumen\Application` class directly.

### Authentication

Since sessions are no longer support in Lumen, authentication is totally based on stateless authentication via API tokens or headers. You should review the [full authentication documentation](/docs/{{version}}/authentication) for more information on how to use the authentication system.

### Collections

#### Eloquent Base Collections

The Eloquent collection instance now returns a base Collection (`Illuminate\Support\Collection`) for the following methods: `pluck`, `keys`, `zip`, `collapse`, `flatten`, `flip`.

#### Key Preservation

The `slice`, `chunk`, and `reverse` methods now preserve keys on the collection. If you do not want these methods to preserve keys, use the `values` method on the `Collection` instance.

### Database

#### MySQL Dates

Starting with MySQL 5.7, `0000-00-00 00:00:00` is no longer considered a valid date, since `strict` mode is enabled by default. All timestamp columns should receive a valid default value when you insert records into your database. You may use the `useCurrent` method in your migrations to default the timestamp columns to the current timestamps, or you may make the timestamps `nullable` to allow `null` values:

    $table->timestamp('foo')->nullable();

    $table->timestamp('foo')->useCurrent();

    $table->nullableTimestamps();

#### MySQL JSON Column Type

The `json` column type now creates actual JSON columns when used by the MySQL driver. If you are not running MySQL 5.7 or above, this column type will not be available to you. Instead, use the `text` column type in your migration.

### Eloquent

#### Date Casts

Any attributes that have been added to your `$casts` property as `date` or `datetime` will now be converted to a string when `toArray` is called on the model or collection of models. This makes the date casting conversion consistent with dates specified in your `$dates` array.

#### Global Scopes

The global scopes implementation has been re-written to be much easier to use. Your global scopes no longer need a `remove` method, so it may be removed from any global scopes you have written.

If we were calling `getQuery` on an Eloquent query builder to access the underlying query builder instance, you should now call `toBase`.

If you were calling the `remove` method directly for any reason, you should change this call to `$eloquentBuilder->withoutGlobalScope($scope)`.

New methods `withoutGlobalScope` and `withoutGlobalScopes` have been added to the Eloquent query builder. Any calls to `$model->removeGlobalScopes($builder)` may be changed to simply `$builder->withoutGlobalScopes()`.

#### Primary keys

By default, Eloquent assumes your primary keys are integers and will automatically cast them to integers. For any primary key that is not an integer you should override the `$incrementing` property on your Eloquent model to `false`:

    /**
     * Indicates if the IDs are auto-incrementing.
     *
     * @var bool
     */
    public $incrementing = true;

### Exception Handling

Your `App\Exceptions\Handler` class' `$dontReport` property should be updated to include at least the following exception types:

    use Illuminate\Validation\ValidationException;
    use Illuminate\Auth\Access\AuthorizationException;
    use Illuminate\Database\Eloquent\ModelNotFoundException;
    use Symfony\Component\HttpKernel\Exception\HttpException;

    /**
     * A list of the exception types that should not be reported.
     *
     * @var array
     */
    protected $dontReport = [
        AuthorizationException::class,
        HttpException::class,
        ModelNotFoundException::class,
        ValidationException::class,
    ];

### IronMQ

The IronMQ queue driver has been moved into its own package and is no longer shipped with the core framework.

[http://github.com/LaravelCollective/iron-queue](http://github.com/laravelcollective/iron-queue)

### Storage

If you made use of Laravel's Flysystem integration, you will need to register the `filesystem` binding. Add the following code to your `bootstrap/app.php`:

    $app->singleton('filesystem', function ($app) {
        return $app->loadComponent(
            'filesystems',
            Illuminate\Filesystem\FilesystemServiceProvider::class,
            'filesystem'
        );
    });

### Validation

The `ValidatesRequests` trait has been merged into the `ProvidesConvenienceMethods` trait used by Lumen's base controller.

If you previously used the `ValidatesRequests` trait outside of the BaseController, you may copy it [from the 5.1 branch](https://github.com/laravel/lumen-framework/blob/5.1/src/Routing/ValidatesRequests.php) or use the full `ProvidesConvenienceMethods` trait.

### Testing

The `DatabaseMigrations` and `DatabaseTransactions` traits have moved from `Illuminate\Foundation\Testing\DatabaseMigrations` and `Illuminate\Foundation\Testing\DatabaseTransactions` to a new location. Update your tests to import the new namespace:

    <?php

    use Laravel\Lumen\Testing\DatabaseMigrations;
    use Laravel\Lumen\Testing\DatabaseTransactions;

    class ExampleTest extends TestCase
    {
        use DatabaseMigrations;
    }
