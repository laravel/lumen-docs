# Release Notes

- [5.1.0](#5.1.0)
- [5.0.4](#5.0.4)
- [5.0 (Based On Laravel 5.0.x)](#5.0)

<a name="5.1.0"></a>
## Lumen 5.1.0

Lumen 5.1.0 upgrades the framework to use the 5.1 family of Laravel components. Features such as event broadcasting, middleware parameters, and testing improvements are now available in Lumen. For the full Laravel 5.1 release notes, consult the [Laravel documentation](http://laravel.com/docs/releases).

<a name="5.0.4"></a>
## Lumen 5.0.4

When upgrading to Lumen 5.0.4, you should update your `bootstrap/app.php` file's creation of the Lumen application class to the following:

	$app = new Laravel\Lumen\Application(
		realpath(__DIR__.'/../')
	);

> **Note:** This is not a **required** change; however, it should prevent some bugs when using the Artisan CLI and PHP's built-in web server.

<a name="5.0"></a>
## Lumen 5.0

Lumen 5.0 is the initial release of the Lumen framework, and is based on the Laravel 5.x series of PHP components.
