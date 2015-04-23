# Release Notes

- [5.0.4](#5.0.4)
- [5.0 (Based On Laravel 5.x)](#5.0)

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
