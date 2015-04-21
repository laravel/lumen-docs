# Configuration

- [Introduction](#introduction)
- [After Installation](#after-installation)
- [Configuration Files](#configuration-files)
- [Pretty URLs](#pretty-urls)

<a name="introduction"></a>
## Introduction

Unlike Laravel, Lumen only uses a single `.env` configuration file which can be used to configure the various aspects of the framework. The `.env.example` that ships with the framework can be used as a starting-point for your Lumen configuration.

> **Note:** If you would like to use the `vlucas/phpdotenv` library to load your environment variables into the `$_ENV` PHP super-global, you should uncomment the call to `Dotenv::load` in your `bootstrap/app.php` file.

<a name="after-installation"></a>
## After Installation

Lumen needs very little configuration out of the box. However, you should set your `APP_KEY` configuration option in the `.env` file. This value should be a 32 character, random string.

However, you may also want to configure a few additional components of Laravel, such as:

- [Cache](/docs/cache#configuration)
- [Database](/docs/database#configuration)
- [Queue](/docs/queues#configuration)
- [Session](/docs/session#configuration)

> **Note:** You should never have the `APP_DEBUG` configuration option set to `true` for a production application.

<a name="permissions"></a>
### Permissions

Laravel may require some permissions to be configured: folders within `storage` and the `bootstrap/cache` directory require write access by the web server.

<a name="configuration-files"></a>
## Configuration Files

By default, Lumen uses a single `.env` file to configure your application. However, you may use full, "Laravel style" configuration files if you wish. The default configuration files are stored in `vendor/laravel/lumen-framework/config` directory. Lumen will use your copy of the configuration file if you copy and paste one of the files into a `config` directory within your project root.

Using full configuration files will give you more control over some aspects of Lumen's configuration, such as configuring multiple storage "disks" or read / write database connections.

#### Custom Configuration Files

You may also create your own custom configuration files and load them using the `$app->configure()` method. For example, if your configuration file is located in `config/options.php`, you can load the file like so:

	$app->configure('options');

<a name="pretty-urls"></a>
## Pretty URLs

### Apache

The framework ships with a `public/.htaccess` file that is used to allow URLs without `index.php`. If you use Apache to serve your Laravel application, be sure to enable the `mod_rewrite` module.

If the `.htaccess` file that ships with Laravel does not work with your Apache installation, try this one:

	Options +FollowSymLinks
	RewriteEngine On

	RewriteCond %{REQUEST_FILENAME} !-d
	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteRule ^ index.php [L]

If your web host doesn't allow the `FollowSymlinks` option, try replacing it with `Options +SymLinksIfOwnerMatch`.

### Nginx

On Nginx, the following directive in your site configuration will allow "pretty" URLs:

	location / {
		try_files $uri $uri/ /index.php?$query_string;
	}

Of course, when using [Homestead](http://laravel.com/docs/homestead), pretty URLs will be configured automatically.
