# Service Container

- [Introduction](#introduction)
- [Basic Usage](#basic-usage)
- [Binding Interfaces To Implementations](#binding-interfaces-to-implementations)
- [Contextual Binding](#contextual-binding)
- [Tagging](#tagging)
- [Container Events](#container-events)

<a name="introduction"></a>
## Introduction

Lumen utilizes the powerful Laravel service container, which is an amazing tool for managing class dependencies. Dependency injection is a fancy word that essentially means this: class dependencies are "injected" into the class via the constructor or, in some cases, "setter" methods.

<a name="basic-usage"></a>
## Basic Usage

> **Note:** To better organize your container bindings, consider placing them in [service providers](/docs/providers).

#### Registering A Basic Resolver

There are several ways the service container can register dependencies, including Closure callbacks and binding interfaces to implementations. First, we'll explore Closure callbacks. A Closure resolver is registered in the container with a key (typically the class name) and a Closure that returns some value:

	$app->bind('FooBar', function($app) {
		return new FooBar($app['SomethingElse']);
	});

#### Registering A Singleton

Sometimes, you may wish to bind something into the container that should only be resolved once, and the same instance should be returned on subsequent calls into the container:

	$app->singleton('FooBar', function($app) {
		return new FooBar($app['SomethingElse']);
	});

#### Binding An Existing Instance Into The Container

You may also bind an existing object instance into the container using the `instance` method. The given instance will always be returned on subsequent calls into the container:

	$fooBar = new FooBar(new SomethingElse);

	$app->instance('FooBar', $fooBar);

### Resolving

There are several ways to resolve something out of the container. First, you may use the `make` method:

	$fooBar = $app->make('FooBar');

#### Automatic Resolution

Secondly, but importantly, you may simply "type-hint" the dependency in the constructor of a class that is resolved by the container, including controllers, event listeners, queue jobs, and more. The container will automatically inject the dependencies:

	<?php namespace App\Http\Controllers;

	use Laravel\Lumen\Routing\Controller;
	use App\Users\Repository as UserRepository;

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

		/**
		 * Show the user with the given ID.
		 *
		 * @param  int  $id
		 * @return Response
		 */
		public function show($id)
		{
			//
		}

	}

<a name="binding-interfaces-to-implementations"></a>
## Binding Interfaces To Implementations

A very powerful feature of the service container is its ability to bind an interface to a given implementation. For example, perhaps our application integrates with the [Pusher](https://pusher.com) web service for sending and receiving real-time events. If we are using Pusher's PHP SDK, we could inject an instance of the Pusher client into a class. First, let's register a binding for the SDK that binds it to an interface:

	$app->bind('App\Contracts\EventPusher', 'App\Services\PusherEventPusher');

This tells the container that it should inject the `PusherEventPusher` when a class needs an implementation of `EventPusher`. Now we can type-hint the `EventPusher` interface in our constructor:

		/**
		 * Create a new order handler instance.
		 *
		 * @param  EventPusher  $pusher
		 * @return void
		 */
		public function __construct(EventPusher $pusher)
		{
			$this->pusher = $pusher;
		}

<a name="contextual-binding"></a>
## Contextual Binding

Sometimes you may have two classes that utilize the same interface, but you wish to inject different implementations into each class. For example, when our system receives a new Order, we may want to send an event via [PubNub](http://www.pubnub.com/) rather than Pusher. Lumen provides a simple, fluent interface for defining this behavior:

	$app->when('App\Service\CreateOrder')
	          ->needs('App\Contracts\EventPusher')
	          ->give('App\Services\PubNubEventPusher');

<a name="tagging"></a>
## Tagging

Occasionally, you may need to resolve all of a certain "category" of binding. For example, perhaps you are building a report aggregator that receives an array of many different `Report` interface implementations. After registering the `Report` implementations, you can assign them a tag using the `tag` method:

	$app->bind('SpeedReport', function() {
		//
	});

	$app->bind('MemoryReport', function() {
		//
	});

	$app->tag(['SpeedReport', 'MemoryReport'], 'reports');

Once the services have been tagged, you may easily resolve them all via the `tagged` method:

	$app->bind('ReportAggregator', function($app) {
		return new ReportAggregator($app->tagged('reports'));
	});

<a name="container-events"></a>
## Container Events

#### Registering A Resolving Listener

The container fires an event each time it resolves an object. You may listen to this event using the `resolving` method:

	$app->resolving(function($object, $app) {
		// Called when container resolves object of any type...
	});

	$app->resolving(function(FooBar $fooBar, $app) {
		// Called when container resolves objects of type "FooBar"...
	});

The object being resolved will be passed to the callback.
