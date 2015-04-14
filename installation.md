# Installation

- [Install Composer](#install-composer)
- [Install Lumen](#install-lumen)
- [Server Requirements](#server-requirements)

<a name="install-composer"></a>
## Install Composer

Lumen utilizes [Composer](http://getcomposer.org) to manage its dependencies. So, before using Lumen, you will need to make sure you have Composer installed on your machine.

<a name="install-lumen"></a>
## Install Lumen

### Via Lumen Installer

First, download the Lumen installer using Composer.

	composer global require "laravel/lumen-installer=~1.0"

Make sure to place the `~/.composer/vendor/bin` directory in your PATH so the `lumen` executable can be located by your system.

Once installed, the simple `lumen new` command will create a fresh Lumen installation in the directory you specify. For instance, `lumen new service` would create a directory named `service` containing a fresh Lumen installation with all dependencies installed. This method of installation is much faster than installing via Composer:

	lumen new service

### Via Composer Create-Project

You may also install Lumen by issuing the Composer `create-project` command in your terminal:

	composer create-project laravel/lumen --prefer-dist

<a name="server-requirements"></a>
## Server Requirements

The Lumen framework has a few system requirements:

- PHP >= 5.4
- Mcrypt PHP Extension
- OpenSSL PHP Extension
- Mbstring PHP Extension
- Tokenizer PHP Extension

<a name="configuration"></a>
## Configuration

Lumen needs almost no other configuration out of the box. You are free to get started developing!

You may also want to configure a few additional components of Lumen, such as:

- [Cache](/docs/cache#configuration)
- [Database](/docs/database#configuration)
- [Queue](/docs/queues#configuration)
- [Session](/docs/session#configuration)

<a name="permissions"></a>
### Permissions

Lumen may require some permissions to be configured: folders within `storage` directory need to be writable.

<a name="pretty-urls"></a>
## Pretty URLs

### Apache

The framework ships with a `public/.htaccess` file that is used to allow URLs without `index.php`. If you use Apache to serve your Lumen application, be sure to enable the `mod_rewrite` module.

If the `.htaccess` file that ships with Lumen does not work with your Apache installation, try this one:

	Options +FollowSymLinks
	RewriteEngine On

	RewriteCond %{REQUEST_FILENAME} !-d
	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteRule ^ index.php [L]

### Nginx

On Nginx, the following directive in your site configuration will allow "pretty" URLs:

	location / {
		try_files $uri $uri/ /index.php?$query_string;
	}

Of course, when using [Homestead](http://laravel.com/docs/homestead), pretty URLs will be configured automatically.
