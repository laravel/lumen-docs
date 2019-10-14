# Database

- [Configuration](#configuration)
- [Basic Usage](#basic-usage)
- [Migrations](#migrations)

<a name="configuration"></a>
## Configuration

Lumen makes connecting with databases and running queries extremely simple. Currently Lumen supports four database systems: MySQL, PostgreSQL, SQLite, and SQL Server.

You may use the `DB_*` configuration options in your `.env` configuration file to configure your database settings, such as the driver, host, username, and password.

<a name="basic-usage"></a>
## Basic Usage

> **Note:** If you would like to use the `DB` facade, you should uncomment the `$app->withFacades()` call in your `bootstrap/app.php` file.

For example, without facades enabled, you may access a database connection via the `app` helper:

    $results = app('db')->select("SELECT * FROM users");

Or, with facades enabled, you may access the database connection via the `DB` facade:

    $results = DB::select("SELECT * FROM users");


#### Basic Queries

To learn how to execute basic, raw SQL queries via the database component, you may consult the [full Laravel documentation](http://laravel.com/docs/database).

#### Query Builder

Lumen may also utilize the Laravel fluent query builder. To learn more about this feature, consult the [full Laravel documentation](http://laravel.com/docs/queries).

#### Eloquent ORM

If you would like to use the Eloquent ORM, you should uncomment the `$app->withEloquent()` call in your `bootstrap/app.php` file.

Of course, you may easily use the full Eloquent ORM with Lumen. To learn how to use Eloquent, check out the [full Laravel documentation](http://laravel.com/docs/eloquent).

<a name="migrations"></a>
## Migrations

For further information on how to create database tables and run migrations, check out the Laravel documentation on the  [migrations](http://laravel.com/docs/migrations).
