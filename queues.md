# Queues

- [Introduction](#introduction)
- [Writing Job Classes](#writing-job-classes)
	- [Job Class Structure](#job-class-structure)
- [Pushing Jobs Onto The Queue](#pushing-jobs-onto-the-queue)
	- [Delayed Jobs](#delayed-jobs)
	- [Dispatching Jobs From Requests](#dispatching-jobs-from-requests)
- [Running The Queue Listener](#running-the-queue-listener)
	- [Supervisor Configuration](#supervisor-configuration)
	- [Daemon Queue Listener](#daemon-queue-listener)
	- [Deploying With Daemon Queue Listeners](#deploying-with-daemon-queue-listeners)
- [Dealing With Failed Jobs](#dealing-with-failed-jobs)
	- [Failed Job Events](#failed-job-events)
	- [Retrying Failed Jobs](#retrying-failed-jobs)

<a name="introduction"></a>
## Introduction

The Lumen queue service provides a unified API across a variety of different queue back-ends. Queues allow you to defer the processing of a time consuming task, such as sending an e-mail, until a later time which drastically speeds up web requests to your application.

<a name="configuration"></a>
### Configuration

The `QUEUE_DRIVER` option in your `.env` file determines the queue "driver" that will be used by your application.

### Driver Prerequisites

#### Database

In order to use the `database` queue driver, you will need a database table to hold the jobs. To generate a migration that creates this table, run the `queue:table` Artisan command. Once the migration is created, you may migrate your database using the `migrate` command:

	php artisan queue:table

	php artisan migrate

#### Other Queue Dependencies

The following dependencies are needed for the listed queue drivers:

- Amazon SQS: `aws/aws-sdk-php ~3.0`
- Beanstalkd: `pda/pheanstalk ~3.0`
- IronMQ: `iron-io/iron_mq ~2.0`
- Redis: `predis/predis ~1.0`

<a name="writing-job-classes"></a>
## Writing Job Classes

<a name="job-class-structure"></a>
### Job Class Structure

By default, all of the queueable jobs for your application are stored in the `app/Jobs` directory. Job classes are very simple, normally containing only a `handle` method which is called when the job is processed by the queue. To get started, let's take a look at an example job class:

	<?php

	namespace App\Jobs;

	use Mail;
	use App\User;
	use App\Jobs\Job;
	use Illuminate\Contracts\Mail\Mailer;
	use Illuminate\Queue\SerializesModels;
	use Illuminate\Queue\InteractsWithQueue;
	use Illuminate\Contracts\Bus\SelfHandling;
	use Illuminate\Contracts\Queue\ShouldQueue;

	class SendReminderEmail extends Job implements SelfHandling, ShouldQueue
	{
	    use InteractsWithQueue, SerializesModels;

	    protected $user;

	    /**
	     * Create a new job instance.
	     *
	     * @param  User  $user
	     * @return void
	     */
	    public function __construct(User $user)
	    {
	        $this->user = $user;
	    }

	    /**
	     * Execute the job.
	     *
	     * @param  Mailer  $mailer
	     * @return void
	     */
	    public function handle(Mailer $mailer)
	    {
	    	$mailer->send('emails.reminder', ['user' => $this->user], function ($m) {
	    		//
	    	});

	    	$user->reminders()->create(...);
	    }
	}

In this example, note that we were able to pass an [Eloquent model](/docs/eloquent) directly into the queued job's constructor. Because of the `SerializesModels` trait that the job is using, Eloquent models will be gracefully serialized and unserialized when the job is processing. If your queued job accepts an Eloquent model in its constructor, only the identifier for the model will be serialized onto the queue. When the job is actually handled, the queue system will automatically re-retrieve the full model instance from the database. It's all totally transparent to your application and prevents issues that can arise from serializing full Eloquent model instances.

The `handle` method is called when the job is processed by the queue. Note that we are able to type-hint dependencies on the `handle` method of the job. The Lumen [service container](/docs/container) automatically injects these dependencies.

#### When Things Go Wrong

If an exception is thrown while the job is being processed, it will automatically be released back onto the queue so it may be attempted again. The job will continue to be released until it has been attempted the maximum number of times allowed by your application. The number of maximum attempts is defined by the `--tries` switch used on the `queue:listen` or `queue:work` Artisan jobs. More information on running the queue listener [can be found below](#running-the-queue-listener).

#### Manually Releasing Jobs

If you would like to `release` the job manually, the `InteractsWithQueue` trait, which is already included in your generated job class, provides access to the queue job `release` method. The `release` method accepts one argument: the number of seconds you wish to wait until the job is made available again:

	public function handle(Mailer $mailer)
	{
		if (condition) {
			$this->release(10);
		}
	}

#### Checking The Number Of Run Attempts

As noted above, if an exception occurs while the job is being processed, it will automatically be released back onto the queue. You may check the number of attempts that have been made to run the job using the `attempts` method:

	public function handle(Mailer $mailer)
	{
		if ($this->attempts() > 3) {
			//
		}
	}

<a name="pushing-jobs-onto-the-queue"></a>
## Pushing Jobs Onto The Queue

The default Lumen controller located in `app/Http/Controllers/Controller.php` uses a `DispatchesJob` trait. This trait provides several methods allowing you to conveniently push jobs onto the queue, such as the `dispatch` method:

	<?php

	namespace App\Http\Controllers;

	use App\User;
	use Illuminate\Http\Request;
	use App\Jobs\SendReminderEmail;
	use App\Http\Controllers\Controller;

	class UserController extends Controller
	{
		/**
		 * Send a reminder e-mail to a given user.
		 *
		 * @param  Request  $request
		 * @param  int  $id
		 * @return Response
		 */
		public function sendReminderEmail(Request $request, $id)
		{
			$user = User::findOrFail($id);

			$this->dispatch(new SendReminderEmail($user));
		}
	}

#### Specifying The Queue For A Job

You may also specify the queue a job should be sent to.

By pushing jobs to different queues, you may "categorize" your queued jobs, and even prioritize how many workers you assign to various queues. This does not push jobs to different queue "connections" as defined by your queue configuration file, but only to specific queues within a single connection. To specify the queue, use the `onQueue` method on the job instance. The `onQueue` method is provided by the base `App\Jobs\Job` class included with Lumen:

	<?php

	namespace App\Http\Controllers;

	use App\User;
	use Illuminate\Http\Request;
	use App\Jobs\SendReminderEmail;
	use App\Http\Controllers\Controller;

	class UserController extends Controller
	{
		/**
		 * Send a reminder e-mail to a given user.
		 *
		 * @param  Request  $request
		 * @param  int  $id
		 * @return Response
		 */
		public function sendReminderEmail(Request $request, $id)
		{
			$user = User::findOrFail($id);

			$job = (new SendReminderEmail($user))->onQueue('emails');

			$this->dispatch($job);
		}
	}

<a name="delayed-jobs"></a>
### Delayed Jobs

Sometimes you may wish to delay the execution of a queued job. For instance, you may wish to queue a job that sends a customer a reminder e-mail 15 minutes after sign-up. You may accomplish this using the `delay` method on your job class, which is provided by the `Illuminate\Bus\Queueable` trait:

	<?php

	namespace App\Http\Controllers;

	use App\User;
	use Illuminate\Http\Request;
	use App\Jobs\SendReminderEmail;
	use App\Http\Controllers\Controller;

	class UserController extends Controller
	{
		/**
		 * Send a reminder e-mail to a given user.
		 *
		 * @param  Request  $request
		 * @param  int  $id
		 * @return Response
		 */
		public function sendReminderEmail(Request $request, $id)
		{
			$user = User::findOrFail($id);

			$job = (new SendReminderEmail($user))->delay(60);

			$this->dispatch($job);
		}
	}

In this example, we're specifying that the job should be delayed in the queue for 60 seconds before being made available to workers.

> **Note:** The Amazon SQS service has a maximum delay time of 15 minutes.

<a name="dispatching-jobs-from-requests"></a>
### Dispatching Jobs From Requests

It is very common to map HTTP request variables into jobs. So, instead of forcing you to do this manually for each request, Lumen provides some helper methods to make it a cinch. Let's take a look at the `dispatchFrom` method available on the `DispatchesJobs` trait. By default, this trait is included on the base Lumen controller class:

	<?php

	namespace App\Http\Controllers;

	use Illuminate\Http\Request;
	use App\Http\Controllers\Controller;

	class CommerceController extends Controller
	{
		/**
		 * Process the given order.
		 *
		 * @param  Request  $request
		 * @param  int  $id
		 * @return Response
		 */
		public function processOrder(Request $request, $id)
		{
			// Process the request...

			$this->dispatchFrom('App\Jobs\ProcessOrder', $request);
		}
	}

This method will examine the constructor of the given job class and extract variables from the HTTP request (or any other `ArrayAccess` object) to fill the needed constructor parameters of the job. So, if our job class accepts a `productId` variable in its constructor, the job bus will attempt to pull the `productId` parameter from the HTTP request.

You may also pass an array as the third argument to the `dispatchFrom` method. This array will be used to fill any constructor parameters that are not available on the request:

	$this->dispatchFrom('App\Jobs\ProcessOrder', $request, [
		'taxPercentage' => 20,
	]);

<a name="running-the-queue-listener"></a>
## Running The Queue Listener

#### Starting The Queue Listener

Lumen includes an Artisan command that will run new jobs as they are pushed onto the queue. You may run the listener using the `queue:listen` command:

	php artisan queue:listen

You may also specify which queue connection the listener should utilize:

	php artisan queue:listen connection

Note that once this task has started, it will continue to run until it is manually stopped. You may use a process monitor such as [Supervisor](http://supervisord.org/) to ensure that the queue listener does not stop running.

#### Queue Priorities

You may pass a comma-delimited list of queue connections to the `listen` job to set queue priorities:

	php artisan queue:listen --queue=high,low

In this example, jobs on the `high` queue will always be processed before moving onto jobs from the `low` queue.

#### Specifying The Job Timeout Parameter

You may also set the length of time (in seconds) each job should be allowed to run:

	php artisan queue:listen --timeout=60

#### Specifying Queue Sleep Duration

In addition, you may specify the number of seconds to wait before polling for new jobs:

	php artisan queue:listen --sleep=5

Note that the queue only "sleeps" if no jobs are on the queue. If more jobs are available, the queue will continue to work them without sleeping.

<a name="supervisor-configuration"></a>
### Supervisor Configuration

Supervisor is a process monitor for the Linux operating system, and will automatically restart your `queue:listen` or `queue:work` commands if they fail. To install Supervisor on Ubuntu, you may use the following command:

	sudo apt-get install supervisor

Supervisor configuration files are typically stored in the `/etc/supervisor/conf.d` directory. Within this directory, you may create any number of configuration files that instruct supervisor how your processes should be monitored. For example, let's create a `laravel-worker.conf` file that starts and monitors a `queue:work` process:

	[program:laravel-worker]
	process_name=%(program_name)s_%(process_num)02d
	command=php /home/forge/app.com/artisan queue:work sqs --sleep=3 --tries=3 --daemon
	autostart=true
	autorestart=true
	user=forge
	numprocs=8
	redirect_stderr=true
	stdout_logfile=/home/forge/app.com/worker.log

In this example, the `numprocs` directive will instruct Supervisor to run 8 `queue:work` processes and monitor all of them, automatically restarting them if they fail. Once the configuration file has been created, you may update the Supervisor configuration and start the processes using the following commands:

	sudo supervisorctl reread

	sudo supervisorctl update

	sudo supervisorctl start laravel-worker

For more information on configuring and using Supervisor, consult the [Supervisor documentation](http://supervisord.org/index.html). Alternatively, you may use [Lumen Forge](https://forge.laravel.com) to automatically configure and manage your Supervisor configuration from a convenient web interface.

<a name="daemon-queue-listener"></a>
### Daemon Queue Listener

The `queue:work` Artisan command includes a `--daemon` option for forcing the queue worker to continue processing jobs without ever re-booting the framework. This results in a significant reduction of CPU usage when compared to the `queue:listen` command:

To start a queue worker in daemon mode, use the `--daemon` flag:

	php artisan queue:work connection --daemon

	php artisan queue:work connection --daemon --sleep=3

	php artisan queue:work connection --daemon --sleep=3 --tries=3

As you can see, the `queue:work` job supports most of the same options available to `queue:listen`. You may use the `php artisan help queue:work` job to view all of the available options.

#### Coding Considerations For Daemon Queue Listeners

Daemon queue workers do not restart the framework before processing each job. Therefore, you should be careful to free any heavy resources before your job finishes. For example, if you are doing image manipulation with the GD library, you should free the memory with `imagedestroy` when you are done.

Similarly, your database connection may disconnect when being used by long-running daemon. You may use the `DB::reconnect` method to ensure you have a fresh connection.

<a name="deploying-with-daemon-queue-listeners"></a>
### Deploying With Daemon Queue Listeners

Since daemon queue workers are long-lived processes, they will not pick up changes in your code without being restarted. So, the simplest way to deploy an application using daemon queue workers is to restart the workers during your deployment script. You may gracefully restart all of the workers by including the following command in your deployment script:

	php artisan queue:restart

This command will gracefully instruct all queue workers to restart after they finish processing their current job so that no existing jobs are lost.

> **Note:** This command relies on the cache system to schedule the restart. By default, APCu does not work for CLI jobs. If you are using APCu, add `apc.enable_cli=1` to your APCu configuration.

<a name="dealing-with-failed-jobs"></a>
## Dealing With Failed Jobs

Since things don't always go as planned, sometimes your queued jobs will fail. Don't worry, it happens to the best of us! Lumen includes a convenient way to specify the maximum number of times a job should be attempted. After a job has exceeded this amount of attempts, it will be inserted into a `failed_jobs` table. The name of the failed jobs can be configured via the `config/queue.php` configuration file.

To create a migration for the `failed_jobs` table, you may use the `queue:failed-table` command:

	php artisan queue:failed-table

When running your [queue listener](#running-the-queue-listener), you may specify the maximum number of times a job should be attempted using the `--tries` switch on the `queue:listen` command:

	php artisan queue:listen connection-name --tries=3

<a name="failed-job-events"></a>
### Failed Job Events

If you would like to register an event that will be called when a queued job fails, you may use the `Queue::failing` method. This event is a great opportunity to notify your team via e-mail or [HipChat](https://www.hipchat.com). For example, we may attach a callback to this event from the `AppServiceProvider` that is included with Lumen:

	<?php

	namespace App\Providers;

	use Queue;
	use Illuminate\Support\ServiceProvider;

	class AppServiceProvider extends ServiceProvider
	{
	    /**
	     * Bootstrap any application services.
	     *
	     * @return void
	     */
		public function boot()
		{
			Queue::failing(function ($connection, $job, $data) {
				// Notify team of failing job...
			});
		}

		/**
		 * Register the service provider.
		 *
		 * @return void
		 */
		public function register()
		{
			//
		}
	}

#### Failed Method On Job Classes

For more granular control, you may define a `failed` method directly on a queue job class, allowing you to perform job specific actions when a failure occurs:

	<?php

	namespace App\Jobs;

	use App\Jobs\Job;
	use Illuminate\Queue\SerializesModels;
	use Illuminate\Queue\InteractsWithQueue;
	use Illuminate\Contracts\Bus\SelfHandling;
	use Illuminate\Contracts\Queue\ShouldQueue;

	class SendReminderEmail extends Job implements SelfHandling, ShouldQueue
	{
	    use InteractsWithQueue, SerializesModels;

	    /**
	     * Execute the job.
	     *
	     * @param  Mailer  $mailer
	     * @return void
	     */
	    public function handle(Mailer $mailer)
	    {
			//
	    }

	    /**
	     * Handle a job failure.
	     *
	     * @return void
	     */
		public function failed()
		{
			// Called when the job is failing...
		}
	}

<a name="retrying-failed-jobs"></a>
### Retrying Failed Jobs

To view all of your failed jobs that have been inserted into your `failed_jobs` database table, you may use the `queue:failed` Artisan command:

	php artisan queue:failed

The `queue:failed` command will list the job ID, connection, queue, and failure time. The job ID may be used to retry the failed job. For instance, to retry a failed job that has an ID of 5, the following command should be issued:

	php artisan queue:retry 5

If you would like to delete a failed job, you may use the `queue:forget` command:

	php artisan queue:forget 5

To delete all of your failed jobs, you may use the `queue:flush` command:

	php artisan queue:flush
