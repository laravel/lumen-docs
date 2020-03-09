# Release Notes

- [7.0.0](#7.0.0)
- [6.0.0](#6.0.0)
- [5.8.0](#5.8.0)
- [5.7.0](#5.7.0)
- [5.6.0](#5.6.0)
- [5.5.0](#5.5.0)
- [5.4.0](#5.4.0)
- [5.3.0](#5.3.0)
- [5.2.0](#5.2.0)
- [5.1.0](#5.1.0)
- [5.0.4](#5.0.4)
- [5.0 (Based On Laravel 5.0.x)](#5.0)

<a name="7.0.0"></a>
## Lumen 7.0.0

Lumen 7.0 upgrades the framework's underlying Laravel components to the `7.x` series.

<a name="6.0.0"></a>
## Lumen 6.0.0

Lumen 6.0 upgrades the framework's underlying Laravel components to the `6.0` series.

<a name="5.8.0"></a>
## Lumen 5.8.0

Lumen 5.8 upgrades the framework's underlying Laravel components to the `5.8` series.

<a name="5.7.0"></a>
## Lumen 5.7.0

Lumen 5.7 upgrades the framework's underlying Laravel components to the `5.7` series.

<a name="5.6.0"></a>
## Lumen 5.6.0

Lumen 5.6 upgrades the framework's underlying Laravel components to the `5.6` series.

<a name="5.5.0"></a>
## Lumen 5.5.0

Lumen 5.5 upgrades the framework's underlying Laravel components to the `5.5` series.

<a name="5.4.0"></a>
## Lumen 5.4.0

Lumen 5.4 upgrades the framework's underlying Laravel components to the `5.4` series.

<a name="5.3.0"></a>
## Lumen 5.3.0

Lumen 5.3 upgrades the framework's underlying Laravel components to the `5.3` series.

<a name="5.2.0"></a>
## Lumen 5.2.0

Lumen 5.2.0 upgrades the framework to use the 5.2 family of Laravel components, as well as introduces some significant changes to Lumen's underlying philosophy and purpose.

### Only Stateless APIs

Lumen 5.2 represents a shift on slimming Lumen to focus solely on serving stateless, JSON APIs. **As such, sessions and views are no longer included with the framework.** If you need access to these features, you should use the full Laravel framework. Upgrading your Lumen application to the full Laravel framework mainly involves copying your routes and classes over into a fresh installation of Laravel. Since Laravel and Lumen share many of the same components, your classes should not require any modification.

### Authentication

Because sessions are no longer included with Lumen, authentication must be done stateless using API tokens or headers. You have complete control over the authentication process in the new `AuthServiceProvider`. Please review the [authentication documentation](/docs/{{version}}/authentication) for more information.

### Testing Helpers

Since sessions and views are no longer included with Lumen, all of the form interaction testing helpers have been removed. The testing helpers for JSON APIs remain, so be sure to review the [testing documentation](/docs/{{version}}/testing).

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
