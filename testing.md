# Testing

- [Introduction](#introduction)
- [Application Testing](#application-testing)
	- [Testing JSON APIs](#testing-json-apis)
	- [Authentication](#authentication)
	- [Custom HTTP Requests](#custom-http-requests)
- [Working With Databases](#working-with-databases)
	- [Resetting The Database After Each Test](#resetting-the-database-after-each-test)
	- [Model Factories](#model-factories)
- [Mocking](#mocking)
	- [Mocking Events](#mocking-events)
	- [Mocking Jobs](#mocking-jobs)
	- [Mocking Facades](#mocking-facades)

<a name="introduction"></a>
## Introduction

Lumen is built with testing in mind. In fact, support for testing with PHPUnit is included out of the box, and a `phpunit.xml` file is already setup for your application. The framework also ships with convenient helper methods allowing you to expressively test your application's JSON responses.

An `ExampleTest.php` file is provided in the `tests` directory. After installing a new Lumen application, simply run `phpunit` on the command line to run your tests.

### Test Environment

Lumen automatically configures the cache to the `array` driver while testing, meaning no cache data will be persisted while testing.

You are free to create other testing environment configurations as necessary. The `testing` environment variables may be configured in the `phpunit.xml` file.

### Defining & Running Tests

To create a test case, simply create a new test file in the `tests` directory. The test class should extend `TestCase`. You may then define test methods as you normally would using PHPUnit. To run your tests, simply execute the `phpunit` command from your terminal:

	<?php

	class FooTest extends TestCase
	{
		public function testSomethingIsTrue()
		{
			$this->assertTrue(true);
		}
	}

> **Note:** If you define your own `setUp` method within a test class, be sure to call `parent::setUp`.

<a name="application-testing"></a>
## Application Testing

Lumen provides a very fluent API for making HTTP requests to your application and examining the output.

<a name="testing-json-apis"></a>
### Testing JSON APIs

Lumen also provides several helpers for testing JSON APIs and their responses. For example, the `get`, `post`, `put`, `patch`, and `delete` methods may be used to issue requests with various HTTP verbs. You may also easily pass data and headers to these methods. To get started, let's write a test to make a `POST` request to `/user` and assert that a given array was returned in JSON format:

	<?php

	class ExampleTest extends TestCase
	{
	    /**
	     * A basic functional test example.
	     *
	     * @return void
	     */
	    public function testBasicExample()
	    {
	    	$this->json('POST', '/user', ['name' => 'Sally'])
	    	     ->seeJson([
	    	     	'created' => true,
	    	     ]);
	    }
	}

The `seeJson` method converts the given array into JSON, and then verifies that the JSON fragment occurs **anywhere** within the entire JSON response returned by the application. So, if there are other properties in the JSON response, this test will still pass as long as the given fragment is present.

#### Verify Exact JSON Match

If you would like to verify that the given array is an **exact** match for the JSON returned by the application, you should use the `seeJsonEquals` method:

	<?php

	class ExampleTest extends TestCase
	{
	    /**
	     * A basic functional test example.
	     *
	     * @return void
	     */
	    public function testBasicExample()
	    {
	    	$this->post('/user', ['name' => 'Sally'])
	    	     ->seeJsonEquals([
	    	     	'created' => true,
	    	     ]);
	    }
	}

<a name="authentication"></a>
### Authentication

The `actingAs` helper method provides a simple way to authenticate a given user as the current user:

	<?php

	class ExampleTest extends TestCase
	{
	    public function testApplication()
	    {
	    	$user = factory('App\User')->create();

			$this->actingAs($user)
			     ->get('/user');
	    }
	}

<a name="custom-http-requests"></a>
### Custom HTTP Requests

If you would like to make a custom HTTP request into your application and get the full `Illuminate\Http\Response` object, you may use the `call` method:

    public function testApplication()
    {
    	$response = $this->call('GET', '/');

    	$this->assertEquals(200, $response->status());
    }

If you are making `POST`, `PUT`, or `PATCH` requests you may pass an array of input data with the request. Of course, this data will be available in your routes and controller via the [Request instance](/docs/{{version}}/requests):

   	$response = $this->call('POST', '/user', ['name' => 'Taylor']);

If you wish to use `assertJsonValidationErrors` then you will need to pass null as the response
key as lumen sets the errors at the root of the response by default:

    public function testApplication()
    {
    	$response = $this->call('POST', '/user', ['name' => null]);

	$response->assertJsonValidationErrors('name', null);
    }

<a name="working-with-databases"></a>
## Working With Databases

Lumen also provides a variety of helpful tools to make it easier to test your database driven applications. First, you may use the `seeInDatabase` helper to assert that data exists in the database matching a given set of criteria. For example, if we would like to verify that there is a record in the `users` table with the `email` value of `sally@example.com`, we can do the following:

    public function testDatabase()
    {
    	// Make call to application...

    	$this->seeInDatabase('users', ['email' => 'sally@foo.com']);
    }

Of course, the `seeInDatabase` method and other helpers like it are for convenience. You are free to use any of PHPUnit's built-in assertion methods to supplement your tests.

<a name="resetting-the-database-after-each-test"></a>
### Resetting The Database After Each Test

It is often useful to reset your database after each test so that data from a previous test does not interfere with subsequent tests.

#### Using Migrations

One option is to rollback the database after each test and migrate it before the next test. Lumen provides a simple `DatabaseMigrations` trait that will automatically handle this for you. Simply use the trait on your test class:

	<?php

	use Laravel\Lumen\Testing\DatabaseMigrations;
	use Laravel\Lumen\Testing\DatabaseTransactions;

	class ExampleTest extends TestCase
	{
		use DatabaseMigrations;

	    /**
	     * A basic functional test example.
	     *
	     * @return void
	     */
	    public function testBasicExample()
	    {
	    	$this->get('/foo');
	    }
	}

#### Using Transactions

Another option is to wrap every test case in a database transaction. Again, Lumen provides a convenient `DatabaseTransactions` trait that will automatically handle this:

	<?php

	use Laravel\Lumen\Testing\DatabaseMigrations;
	use Laravel\Lumen\Testing\DatabaseTransactions;

	class ExampleTest extends TestCase
	{
		use DatabaseTransactions;

	    /**
	     * A basic functional test example.
	     *
	     * @return void
	     */
	    public function testBasicExample()
	    {
	    	$this->get('/foo');
	    }
	}

<a name="model-factories"></a>
### Model Factories

When testing, it is common to need to insert a few records into your database before executing your test. Instead of manually specifying the value of each column when you create this test data, Lumen allows you to define a default set of attributes for each of your [Eloquent models](https://laravel.com/docs/eloquent) using model factories. 

To get started, take a look at the `database/factories/UserFactory.php` file in your application. Out of the box, this file contains the following factory definition:

	<?php
    
    namespace Database\Factories;
    
    use App\Models\User;
    use Illuminate\Database\Eloquent\Factories\Factory;
    
    class UserFactory extends Factory
    {
        /**
         * The name of the factory's corresponding model.
         *
         * @var string
         */
        protected $model = User::class;
    
        /**
         * Define the model's default state.
         *
         * @return array
         */
        public function definition()
        {
            return [
                'name' => $this->faker->name,
                'email' => $this->faker->unique()->safeEmail,
            ];
        }
    }

As you can see, in their most basic form, factories are classes that extend Lumen's base factory class and define a `model` property and `definition` method. The `definition` method returns the default set of attribute values that should be applied when creating a model using the factory.

Via the `faker` property, factories have access to the [Faker](https://github.com/fzaninotto/Faker) PHP library, which allows you to conveniently generate various kinds of random data for testing.

#### Factory States

State manipulation methods allow you to define discrete modifications that can be applied to your model factories in any combination. For example, your `User` model might have a `suspended` state that modifies one of its default attribute values. You may define your state transformations using the base factory's `state` method. You may name your state method anything you like. After all, it's just a typical PHP method:

    /**
     * Indicate that the user is suspended.
     *
     * @return \Illuminate\Database\Eloquent\Factories\Factory
     */
    public function suspended()
    {
        return $this->state([
            'account_status' => 'suspended',
        ]);
    }

If your state transformation requires access to the other attributes defined by the factory, you may pass a callback to the `state` method. The callback will receive the array of raw attributes defined for the factory:

    /**
     * Indicate that the user is suspended.
     *
     * @return \Illuminate\Database\Eloquent\Factories\Factory
     */
    public function suspended()
    {
        return $this->state(function (array $attributes) {
            return [
                'account_status' => 'suspended',
            ];
        });
    }

#### Factory Callbacks

Factory callbacks are registered using the `afterMaking` and `afterCreating` methods and allow you to perform additional tasks after making or creating a model. You should register these callbacks by defining a `configure` method on the factory class. This method will automatically be called by Laravel when the factory is instantiated:

    namespace Database\Factories;

    use App\Models\User;
    use Illuminate\Database\Eloquent\Factories\Factory;
    use Illuminate\Support\Str;

    class UserFactory extends Factory
    {
        /**
         * The name of the factory's corresponding model.
         *
         * @var string
         */
        protected $model = User::class;

        /**
         * Configure the model factory.
         *
         * @return $this
         */
        public function configure()
        {
            return $this->afterMaking(function (User $user) {
                //
            })->afterCreating(function (User $user) {
                //
            });
        }

        // ...
    }

#### Using Factories In Tests

The Lumen model factories based on the same code as the Laravel model factories. Therefore, please consult the [full Laravel documentation](https://laravel.com/docs/8.x/database-testing#using-factories) for usage examples.

<a name="mocking"></a>
## Mocking

<a name="mocking-events"></a>
### Mocking Events

If you are making heavy use of Lumen's event system, you may wish to silence or mock certain events while testing. For example, if you are testing user registration, you probably do not want all of a `UserRegistered` event's handlers firing, since these may send "welcome" e-mails, etc.

Lumen provides a convenient `expectsEvents` method that verifies the expected events are fired, but prevents any handlers for those events from running:

	<?php

	class ExampleTest extends TestCase
	{
	    public function testUserRegistration()
	    {
	    	$this->expectsEvents('App\Events\UserRegistered');

	    	// Test user registration code...
	    }
	}

If you would like to prevent all event handlers from running, you may use the `withoutEvents` method:

	<?php

	class ExampleTest extends TestCase
	{
	    public function testUserRegistration()
	    {
	    	$this->withoutEvents();

	    	// Test user registration code...
	    }
	}

<a name="mocking-jobs"></a>
### Mocking Jobs

Sometimes, you may wish to simply test that specific jobs are dispatched by your controllers when making requests to your application. This allows you to test your routes / controllers in isolation - set apart from your job's logic. Of course, you can then test the job itself in a separate test class.

Lumen provides a convenient `expectsJobs` method that will verify that the expected jobs are dispatched, but the job itself will not be executed:

	<?php

	class ExampleTest extends TestCase
	{
	    public function testPurchasePodcast()
	    {
	    	$this->expectsJobs('App\Jobs\PurchasePodcast');

	    	// Test purchase podcast code...
	    }
	}

> **Note:** This method only detects jobs that are dispatched via the `dispatch` global helper function or the `$this->dispatch` method from a route or controller. It does not detect jobs that are sent directly to `Queue::push`.

<a name="mocking-facades"></a>
### Mocking Facades

When testing, you may often want to mock a call to a Lumen [facade](https://laravel.com/docs/facades). For example, consider the following controller action:

	<?php

	namespace App\Http\Controllers;

	use Cache;

	class UserController extends Controller
	{
		/**
		 * Show a list of all users of the application.
		 *
		 * @return Response
		 */
		public function index()
		{
			$value = Cache::get('key');

			//
		}
	}

We can mock the call to the `Cache` facade by using the `shouldReceive` method, which will return an instance of a [Mockery](https://github.com/padraic/mockery) mock. Since facades are actually resolved and managed by the Lumen [service container](/docs/{{version}}/container), they have much more testability than a typical static class. For example, let's mock our call to the `Cache` facade:

	<?php

	class FooTest extends TestCase
	{
		public function testGetIndex()
		{
			Cache::shouldReceive('get')
						->once()
						->with('key')
						->andReturn('value');

			$this->get('/users');
		}
	}

> **Note:** You should not mock the `Request` facade. Instead, pass the input you desire into the HTTP helper methods such as `call` and `post` when running your test.
