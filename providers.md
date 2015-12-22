# Service Providers

- [Introduction](#introduction)
- [Writing Service Providers](#writing-service-providers)
    - [The Register Method](#the-register-method)
    - [The Boot Method](#the-boot-method)
- [Registering Providers](#registering-providers)

<a name="introduction"></a>
## Introduction

Service providers are the central place of all Lumen application bootstrapping. Your own application, as well as all of Lumen's core services are bootstrapped via service providers.

But, what do we mean by "bootstrapped"? In general, we mean **registering** things, including registering service container bindings, event listeners, middleware, and even routes. Service providers are the central place to configure your application.

If you open the `bootstrap/app.php` file included with Lumen, you will see a call to `$app->register()`. You may add additional calls to this method to register as many service providers as your application requires.

In this overview you will learn how to write your own service providers and register them with your Lumen application.

<a name="writing-service-providers"></a>
## Writing Service Providers

All service providers extend the `Illuminate\Support\ServiceProvider` class. This abstract class requires that you define at least one method on your provider: `register`. Within the `register` method, you should **only bind things into the [service container](/docs/container)**. You should never attempt to register any event listeners, routes, or any other piece of functionality within the `register` method.

<a name="the-register-method"></a>
### The Register Method

As mentioned previously, within the `register` method, you should only bind things into the [service container](/docs/container). You should never attempt to register any event listeners, routes, or any other piece of functionality within the `register` method. Otherwise, you may accidently use a service that is provided by a service provider which has not loaded yet.

Now, let's take a look at a basic service provider:

    <?php

    namespace App\Providers;

    use Riak\Connection;
    use Illuminate\Support\ServiceProvider;

    class RiakServiceProvider extends ServiceProvider
    {
        /**
         * Register bindings in the container.
         *
         * @return void
         */
        public function register()
        {
            $this->app->singleton(Connection::class, function ($app) {
                return new Connection(config('riak'));
            });
        }
    }

This service provider only defines a `register` method, and uses that method to define an implementation of `Riak\Connection` in the service container. If you don't understand how the service container works, check out [its documentation](/docs/container).

<a name="the-boot-method"></a>
### The Boot Method

So, what if we need to register a view composer within our service provider? This should be done within the `boot` method. **This method is called after all other service providers have been registered**, meaning you have access to all other services that have been registered by the framework:

    <?php

    namespace App\Providers;

    use Queue;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        // Other Service Provider Properties...

        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            Queue::failing(function ($event) {

            });
        }
    }

<a name="registering-providers"></a>
## Registering Providers

All service providers are registered in the `bootstrap/app.php` file. This file contains a call to the `$app->register()` method. You may add as many calls to the register method as needed to register all of your providers.
