# Views

## Introduction

Lumen allows you to serve HTML content as part of your application using the View facade and the powerful Blade templating engine.

## Differences From Laravel

Views in Lumen cache drivers utilize the exact same code as the full Laravel view facade. There are no differences between using views in Lumen and using views in Laravel; therefore, please consult the [full Laravel documentation](https://laravel.com/docs/views) for usage examples.

> **Note:** Before using the `View` facade, be sure you have uncommented the `$app->withFacades()` method call in your `bootstrap/app.php` file.
