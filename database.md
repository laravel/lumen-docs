# Database

- [Configuration](#configuration)
- [Basic Usage](#basic-usage)
- [Migrations](#migrations)

<a name="configuration"></a>
## Configuration

Lumen makes connecting with databases and running queries extremely simple. Currently Laravel supports four database systems: MySQL, Postgres, SQLite, and SQL Server.

You may use the `DB_*` configuration options in your `.env` configuration file to configure your database settings, such as the driver, host, username, and password.

> **Note:** In order for your configuration values to be loaded, you will need to uncomment the `Dotenv::load()` method call in your `bootstrap/app.php` file.

<a name="basic-usage"></a>
## Basic Usage

> **Note:** If you would like to use the `DB` facade, you should uncomment the `$app->withFacades()` call in your `bootstrap/app.php` file.

#### Basic Queries

To learn how to execute basic, raw SQL queries via the database component, you may consult the [full Laravel documentation](http://laravel.com/docs/database#running-queries).

#### Query Builder

Lumen may also utilize the Laravel fluent query builder. To learn more about this feature, consult the [full Laravel documentation](http://laravel.com/docs/queries).

#### Eloquent ORM

If you would like to use the Eloquent ORM, you should uncomment the `$app->withEloquent()` call in your `bootstrap/app.php` file.

Of course, you may easily use the full Eloquent ORM with Lumen. To learn how to use Eloquent, check out the [full Laravel documentation](http://laravel.com/docs/eloquent).

<a name="migrations"></a>
## Migrations

If you would like to use database migrations with Lumen, you should run the `php artisan make database` Artisan command. This command will create a `database` directory in your project root which contains `migrations` and `seeds` directories.

For further information on how to create database tables and run migrations, check out the Laravel documentation on the [schema builder](http://laravel.com/docs/schema) and [migrator](http://laravel.com/docs/migrations).
