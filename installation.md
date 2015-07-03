# Installation

- [Installation](#installation)
- [Configuration](#configuration)
	- [Basic Configuration](#basic-configuration)
	- [Environment Configuration](#environment-configuration)
	- [Accessing Configuration Values](#accessing-configuration-values)

<a name="installation"></a>
## Installation

### Server Requirements

The Lumen framework has a few system requirements. Of course, all of these requirements are satisfied by the [Laravel Homestead](http://laravel.com/docs/homestead) virtual machine:

<div class="content-list" markdown="1">
- PHP >= 5.5.9
- OpenSSL PHP Extension
- Mbstring PHP Extension
- Tokenizer PHP Extension
</div>

<a name="install-lumen"></a>
### Installing Lumen

Lumen utilizes [Composer](http://getcomposer.org) to manage its dependencies. So, before using Lumen, make sure you have Composer installed on your machine.

#### Via Lumen Installer

First, download the Lumen installer using Composer:

	composer global require "laravel/lumen-installer=~1.0"

Make sure to place the `~/.composer/vendor/bin` directory in your PATH so the `lumen` executable can be located by your system.

Once installed, the simple `lumen new` command will create a fresh Lumen installation in the directory you specify. For instance, `lumen new blog` will create a directory named `blog` containing a fresh Lumen installation with all of Lumen's dependencies already installed. This method of installation is much faster than installing via Composer:

	lumen new blog

#### Via Composer Create-Project

You may also install Lumen by issuing the Composer `create-project` command in your terminal:

	composer create-project laravel/lumen --prefer-dist

<a name="configuration"></a>
## Configuration

<a name="basic-configuration"></a>
### Basic Configuration

Unlike the full-stack Laravel framework which has multiple configuration files, all of the configuration options for the Lumen framework are stored in a single `.env` configuration file.

#### Application Key

After installing Lumen, you should set your application key to a 32 character, random string. The key can be set in the `.env` environment file. If you have not renamed the `.env.example` file to `.env`, you should do that now. **If the application key is not set, your user sessions and other encrypted data will not be secure!**

#### Additional Configuration

Lumen needs almost no other configuration out of the box. You are free to get started developing!

You may also want to configure a few additional components of Lumen, such as:

- [Cache](/docs/cache#configuration)
- [Database](/docs/database#configuration)

<a name="pretty-urls"></a>
#### Pretty URLs

**Apache**

The framework ships with a `public/.htaccess` file that is used to allow URLs without `index.php`. If you use Apache to serve your Lumen application, be sure to enable the `mod_rewrite` module.

If the `.htaccess` file that ships with Lumen does not work with your Apache installation, try this one:

	Options +FollowSymLinks
	RewriteEngine On

	RewriteCond %{REQUEST_FILENAME} !-d
	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteRule ^ index.php [L]

**Nginx**

On Nginx, the following directive in your site configuration will allow "pretty" URLs:

	location / {
		try_files $uri $uri/ /index.php?$query_string;
	}

Of course, when using [Homestead](http://laravel.com/docs/homestead), pretty URLs will be configured automatically.

<a name="environment-configuration"></a>
### Environment Configuration

It is often helpful to have different configuration values based on the environment the application is running in. For example, you may wish to use a different cache driver locally than you do on your production server. It's easy using environment based configuration.

To make this a cinch, Lumen utilizes the [DotEnv](https://github.com/vlucas/phpdotenv) PHP library by Vance Lucas. In a fresh Lumen installation, the root directory of your application will contain a `.env.example` file. If you install Lumen via Composer, this file will automatically be renamed to `.env`. Otherwise, you should rename the file manually.

All of the variables listed in this file will be loaded into the `$_ENV` PHP super-global when your application receives a request. You may use the `env` helper to retrieve values from these variables. In fact, if you review the Lumen configuration files, you will notice several of the options already using this helper!

Feel free to modify your environment variables as needed for your own local server, as well as your production environment. However, your `.env` file should not be committed to your application's source control, since each developer / server using your application could require a different environment configuration.

If you are developing with a team, you may wish to continue including a `.env.example` file with your application. By putting place-holder values in the example configuration file, other developers on your team can clearly see which environment variables are needed to run your application.

#### Accessing The Current Application Environment

You may access the current application environment via the `environment` method on the `App` facade:

	$environment = App::environment();

You may also pass arguments to the `environment` method to check if the environment matches a given value. You may even pass multiple values if necessary:

	if (App::environment('local')) {
		// The environment is local
	}

	if (App::environment('local', 'staging')) {
		// The environment is either local OR staging...
	}

An application instance may also be accessed via the `app` helper method:

	$environment = app()->environment();

<a name="accessing-configuration-values"></a>
### Accessing Configuration Values

You may easily access your configuration values using the global `config` helper function. The configuration values may be accessed using "dot" syntax, which includes the name of the file and option you with to access. A default value may also be specified and will be returned if the configuration option does not exist:

	$value = config('app.timezone');

To set configuration values at runtime, pass an array to the `config` helper:

	config(['app.timezone' => 'America/Chicago']);
