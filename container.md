# Service Container

- [Binding](#binding)
	- [Binding Interfaces To Implementations](#binding-interfaces-to-implementations)
	- [Contextual Binding](#contextual-binding)
	- [Tagging](#tagging)
- [Resolving](#resolving)
- [Container Events](#container-events)

<a name="binding"></a>
## Binding

Almost all of your service container bindings will be registered within [service providers](/docs/providers), so all of these examples will demonstrate using the container in that context. However, there is no need to bind classes into the container if they do not depend on any interfaces. The container does not need to be instructed how to build these objects, since it can automatically resolve such "concrete" objects using PHP's reflection services.

Within a service provider, you always have access to the container via the `$this->app` instance variable. We can register a binding using the `bind` method, passing the class or interface name that we wish to register along with a `Closure` that returns an instance of the class:

	$this->app->bind('HelpSpot\API', function ($app) {
		return new HelpSpot\API($app['HttpClient']);
	});

Notice that we receive the container itself as an argument to the resolver. We can then use the container to resolve sub-dependencies of the object we are building.

#### Binding A Singleton

The `singleton` method binds a class or interface into the container that should only be resolved one time, and then that same instance will be returned on subsequent calls into the container:

	$this->app->singleton('FooBar', function ($app) {
		return new FooBar($app['SomethingElse']);
	});

#### Binding Instances

You may also bind an existing object instance into the container using the `instance` method. The given instance will always be returned on subsequent calls into the container:

	$fooBar = new FooBar(new SomethingElse);

	$this->app->instance('FooBar', $fooBar);

<a name="binding-interfaces-to-implementations"></a>
### Binding Interfaces To Implementations

A very powerful feature of the service container is its ability to bind an interface to a given implementation. For example, let's assume we have an `EventPusher` interface and a `RedisEventPusher` implementation. Once we have coded our `RedisEventPusher` implementation of this interface, we can register it with the service container like so:

	$this->app->bind('App\Contracts\EventPusher', 'App\Services\RedisEventPusher');

This tells the container that it should inject the `RedisEventPusher` when a class needs an implementation of `EventPusher`. Now we can type-hint the `EventPusher` interface in a constructor, or any other location where dependencies are injected by the service container:

	use App\Contracts\EventPusher;

	/**
	 * Create a new class instance.
	 *
	 * @param  EventPusher  $pusher
	 * @return void
	 */
	public function __construct(EventPusher $pusher)
	{
		$this->pusher = $pusher;
	}

<a name="contextual-binding"></a>
### Contextual Binding

Sometimes you may have two classes that utilize the same interface, but you wish to inject different implementations into each class. For example, when our system receives a new Order, we may want to send an event via [PubNub](http://www.pubnub.com/) rather than Pusher. Lumen provides a simple, fluent interface for defining this behavior:

	$this->app->when('App\Handlers\Commands\CreateOrderHandler')
	          ->needs('App\Contracts\EventPusher')
	          ->give('App\Services\PubNubEventPusher');

You may even pass a Closure to the `give` method:

	$this->app->when('App\Handlers\Commands\CreateOrderHandler')
	          ->needs('App\Contracts\EventPusher')
	          ->give(function () {
	          		// Resolve dependency...
	          	});

<a name="tagging"></a>
### Tagging

Occasionally, you may need to resolve all of a certain "category" of binding. For example, perhaps you are building a report aggregator that receives an array of many different `Report` interface implementations. After registering the `Report` implementations, you can assign them a tag using the `tag` method:

	$this->app->bind('SpeedReport', function () {
		//
	});

	$this->app->bind('MemoryReport', function () {
		//
	});

	$this->app->tag(['SpeedReport', 'MemoryReport'], 'reports');

Once the services have been tagged, you may easily resolve them all via the `tagged` method:

	$this->app->bind('ReportAggregator', function ($app) {
		return new ReportAggregator($app->tagged('reports'));
	});

<a name="resolving"></a>
## Resolving

There are several ways to resolve something out of the container. First, you may use the `make` method, which accepts the name of the class or interface you wish to resolve:

	$fooBar = $this->app->make('FooBar');

Secondly, you may access the container like an array, since it implements PHP's `ArrayAccess` interface:

	$fooBar = $this->app['FooBar'];

Lastly, but most importantly, you may simply "type-hint" the dependency in the constructor of a class that is resolved by the container, including [controllers](/docs/controllers), [event listeners](/docs/events), [queue jobs](/docs/queues), [middleware](/docs/middleware), and more. In practice, this is how most of your objects are resolved by the container.

The container will automatically inject dependencies for the classes it resolves. For example, you may type-hint a repository defined by your application in a controller's constructor. The repository will automatically be resolved and injected into the class:

	<?php

	namespace App\Http\Controllers;

	use App\Users\Repository as UserRepository;

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

<a name="container-events"></a>
## Container Events

The service container fires an event each time it resolves an object. You may listen to this event using the `resolving` method:

	$this->app->resolving(function ($object, $app) {
		// Called when container resolves object of any type...
	});

	$this->app->resolving(function (FooBar $fooBar, $app) {
		// Called when container resolves objects of type "FooBar"...
	});

As you can see, the object being resolved will be passed to the callback, allowing you to set any additional properties on the object before it is given to its consumer.
