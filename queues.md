# Queues

<a name="introduction"></a>
## Introduction

The Lumen queue service provides a unified API across a variety of different queue back-ends. Queues allow you to defer the processing of a time consuming task, such as performing a task on a remote server, until a later time which drastically speeds up web requests to your application.

Like many other parts of the framework, Lumen's queued jobs function identically to Laravel's queued jobs. So, to learn more about queuing jobs in Lumen, please review the [full Laravel queue documentation](https://laravel.com/docs/queues).

<a name="configuration"></a>
### Configuration

The queue configuration options are in the `.env` file. 

If you would like to use the Amazon SQS, Beanstalkd, Iron.io or Redis queue drivers, then you must copy the entire `vendor/laravel/lumen-framework/config/queue.php` file to the `config` directory in the root of your project, and adjust the necessary configuration options as needed. If the `config` directory does not exist, then you must create it first.

### Driver Prerequisites

#### Database

In order to use the `database` queue driver, you will need a database table to hold the jobs:

    Schema::create('jobs', function (Blueprint $table) {
        $table->bigIncrements('id');
        $table->string('queue');
        $table->longText('payload');
        $table->tinyInteger('attempts')->unsigned();
        $table->tinyInteger('reserved')->unsigned();
        $table->unsignedInteger('reserved_at')->nullable();
        $table->unsignedInteger('available_at');
        $table->unsignedInteger('created_at');
        $table->index(['queue', 'reserved', 'reserved_at']);
    });

#### Other Queue Dependencies

The following dependencies are needed for the listed queue drivers:

- Amazon SQS: `aws/aws-sdk-php ~3.0`
- Beanstalkd: `pda/pheanstalk ~3.0`
- Redis: `predis/predis ~1.0`

## Differences From Laravel

Like many other parts of the framework, Lumen's queued jobs function identically to Laravel's queued jobs. So, to learn more about queuing jobs in Lumen, please review the [full Laravel queue documentation](https://laravel.com/docs/queues).

However, there are a few minor differences that we will discuss now. First, let's talk about how queued jobs are generated in Lumen.

#### Generators

Lumen does not include generators for automatically creating new Job classes. Instead, you should copy the `ExampleJob` class that is included with the framework. This class provides the basic structure that is shared by every Job class. The base `Job` that is used by the `ExampleJob` already includes the needed `InteractsWithQueue`, `Queueable`, and `SerializesModels` traits:

    <?php

    namespace App\Jobs;

    class ExampleJob extends Job
    {
        /**
         * Create a new job instance.
         *
         * @return void
         */
        public function __construct()
        {
            //
        }

        /**
         * Execute the job.
         *
         * @return void
         */
        public function handle()
        {
            //
        }
    }

#### Dispatching Jobs

Again, you should consult the full Laravel documentation for complete information on dispatching queued jobs; however, just like in the Laravel framework, you may use the `dispatch` function to dispatch jobs from anywhere within your Lumen application:

    dispatch(new ExampleJob);

Of course, you may also use the `Queue` facade. If you choose to use the facade, be sure to uncomment the call to `$app->withFacades()` in your `bootstrap/app.php` file:

    Queue::push(new ExampleJob);
