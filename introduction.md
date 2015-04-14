# Introduction

- [What Is Lumen?](#what-is-lumen)
- [When Should I Use Lumen?](#when-should-you-use-lumen)
- [Which Laravel Features Does Lumen Include?](#lumen-features)

<a name="what-is-lumen"></a>
## What Is Lumen?

Lumen is a "micro-framework" built on top of Laravel's components, and is the official micro-framework of Laravel. Lumen is built for speed, and is one of the fastest PHP micro-frameworks available - even significantly faster than similar frameworks such as Silex.

However, unlike many other micro-frameworks, Lumen lets you tap into the full power of Laravel's features, such as routing, dependency injection, the Eloquent ORM, migrations, queued jobs, and even scheduled commands.

Laravel is already fast and powerful, but Lumen strips away many of the configuration and customization options that Laravel provides in order to shave every millisecond possible off of your service's load time.

The stunningly fast speed of Lumen, combined with the convenience of Laravel's features gives you a "best of both worlds" micro-framework that is truly a joy to work with.

<a name="when-should-i-use-lumen"></a>
## When Should I Use Lumen?

Lumen is designed to build blazing fast micro-services and APIs. For example, if there is one aspect of your Laravel application that receives drastically more traffic than the rest of the application, you may choose to build that aspect of the application as a small, separate Lumen application.

By reducing the load on your primary Laravel application, you can cut server costs since applications built on Lumen do not require as much server power as a full Laravel application.

Of course, Lumen applications can queue jobs for your main Laravel application to process. Laravel and Lumen are designed to make a perfect team, and, when used together, allow you to build powerful, micro-service driven applications.

Lumen is also a great fit for building fast JSON APIs since these applications do not typically require many "full-stack" features such as HTTP sessions, cookies, and templating.

### Lumen Limitations

Lumen is not as configurable as the Laravel framework. For example, it is not possible to override any framework "bootstrappers" to drastically alter how the framework is constructed. Also, unlike Laravel, Lumen is not intended to be used with additional Laravel "packages" such as debug bars, CMS systems, etc.

In addition, Lumen does not use Symfony's Routing component. Instead, `nikic/fast-route` is used for greater performance. If you need Symfony Routing features such as sub-domain routing or optional parameters, you should use the full Laravel framework.

If you do choose to use the full-stack Laravel framework, do not worry that your application will suffer from poor performance. The full-stack Laravel framework powers many very large, enterprise level applications receiving up to 15,000,000 requests per day.

<a name="lumen-features"></a>
## Lumen Features

Lumen includes many of the same features as the full-stack Laravel framework:

- Blade Templating
- Caching
- Command Scheduler
- Controllers
- Eloquent ORM
- Error Handling
- Database Abstraction
- Dependency Injection
- Logging
- Queued Jobs

By utilizing a unique bootstrapping process, Lumen is able to provide a robust feature set while still delivering extremely high performance, making it the perfect solution for PHP micro-services.

Of course, you may explore the documentation for each of these features (and others) by browsing this documentation.
