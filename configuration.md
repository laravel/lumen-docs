# Configuration

- [Introduction](#introduction)
- [Accessing Configuration Values](#accessing-configuration-values)
- [Environment Configuration](#environment-configuration)
    - [Determining The Current Environment](#determining-the-current-environment)

<a name="introduction"></a>
## Introduction

All of the configuration options for the Lumen framework are stored in the `.env` file.

<a name="accessing-configuration-values"></a>
## Accessing Configuration Values

You may easily access your configuration values using the global `config` helper function from anywhere in your application. The configuration values may be accessed using "dot" syntax, which includes the name of the file and option you wish to access. A default value may also be specified and will be returned if the configuration option does not exist:

    $value = config('app.locale');

To set configuration values at runtime, pass an array to the `config` helper:

    config(['app.locale' => 'en']);

Before a configuration file can be used, you should load it into the application using the `configure` method. This may be done within your `bootstrap/app.php` file:

    $app->configure('app');

<a name="environment-configuration"></a>
## Environment Configuration

It is often helpful to have different configuration values based on the environment the application is running in. For example, you may wish to use a different cache driver locally than you do on your production server. It's easy using environment based configuration.

To make this a cinch, Lumen utilizes the [DotEnv](https://github.com/vlucas/phpdotenv) PHP library by Vance Lucas. In a fresh Lumen installation, the root directory of your application will contain a `.env.example` file. You should rename the `.env.example` file to `.env` when creating your application.

All of the variables listed in this file will be loaded into the `$_ENV` PHP super-global when your application receives a request. The `env` function may be used to retrieve the values of your environment variables:

    $debug = env('APP_DEBUG', true);

The second value passed to the `env` function is the "default value". This value will be used if no environment variable exists for the given key.

Your `.env` file should not be committed to your application's source control, since each developer / server using your application could require a different environment configuration.

If you are developing with a team, you may wish to continue including a `.env.example` file with your application. By putting place-holder values in the example configuration file, other developers on your team can clearly see which environment variables are needed to run your application.

<a name="determining-the-current-environment"></a>
### Determining The Current Environment

The current application environment is determined via the `APP_ENV` variable from your `.env` file. You may access this value via the `environment` method on the application instance:

    $environment = app()->environment();

You may also pass arguments to the `environment` method to check if the environment matches a given value. If necessary, you may even pass multiple values to the `environment` method. If the environment matches any of the given values, the method will return `true`:

    if (app()->environment('local')) {
        // The environment is local
    }

    if (app()->environment('local', 'staging')) {
        // The environment is either local OR staging...
    }
