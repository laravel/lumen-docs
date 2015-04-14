# Service Providers

- [Introduction](#introduction)
- [Basic Provider Example](#basic-provider-example)
- [Registering Providers](#registering-providers)

<a name="introduction"></a>
## Introduction

Service providers are the central place of all Lumen application bootstrapping. Your own application, as well as all of Lumen's core services are bootstrapped via service providers.

But, what do we mean by "bootstrapped"? In general, we mean **registering** things, including registering service container bindings and event listeners. Service providers are the central place to configure your application.

If you open the `bootstrap/app.php` file included with Laravel, you will see a call to `$app->register()`. You may add additional calls to this method to register additional service providers.

In this overview you will learn how to write your own service providers and register them with your Lumen application.

<a name="basic-provider-example"></a>
## Basic Provider Example

All service providers extend the `Illuminate\Support\ServiceProvider` class. This abstract class requires that you define at least one method on your provider: `register`.

### The Register Method

Now, let's take a look at a basic service provider:

	<?php namespace App\Providers;

	use Riak\Connection;
	use Illuminate\Support\ServiceProvider;

	class RiakServiceProvider extends ServiceProvider {

		/**
		 * Register bindings in the container.
		 *
		 * @return void
		 */
		public function register()
		{
			$this->app->singleton('Riak\Contracts\Connection', function($app) {
				return new Connection($app['config']['riak']);
			});
		}

	}

This service provider only defines a `register` method, and uses that method to define an implementation of `Riak\Contracts\Connection` in the service container. If you don't understand how the service container works, don't worry, [we'll cover that soon](/docs/container).

This class is namespaced under `App\Providers` since that is the default location for service providers in Laravel. However, you are free to change this as you wish. Your service providers may be placed anywhere that Composer can autoload them.

<a name="registering-providers"></a>
## Registering Providers

All service providers are registered in the `bootstrap/app.php` bootstrap file. This file contains a sample call to `$app->register()`.

To register your provider, simply add another call to this method:

	$app->register('App\Providers\YourServiceProvider');
