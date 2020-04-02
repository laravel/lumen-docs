# Mail

## Introduction

Lumen provides a clean, simple API over the popular [SwiftMailer](https://swiftmailer.symfony.com/) library with drivers for SMTP, Mailgun, SparkPost, Amazon SES, PHP's `mail` function, and `sendmail`, allowing you to quickly get started sending mail through a local or cloud based service of your choice.

### Configuration

Before using Laravel's mail drivers in Lumen, you will need to install the `illuminate/mail` package via Composer. Then, you should register the `Illuminate\Mail\MailServiceProvider` in your `bootstrap/app.php` file:

    $app->register(Illuminate\Mail\MailServiceProvider::class);

Next, configure your mail options by copying the `mail.php` configuration file from the [full Laravel framework](https://github.com/laravel/laravel/blob/master/config/mail.php) to the `config` directory in the root of your project. Of course, you are free to adjust the configuration options as needed. If your Lumen application does not contain a `config` directory, you should create it.

After creating the configuration file, you should register the mailer and its aliases within your `bootstrap/app.php` file:

    $app->configure('mail');
    
    $app->alias('mail.manager', Illuminate\Mail\MailManager::class);
    $app->alias('mail.manager', Illuminate\Contracts\Mail\Factory::class);
    
    $app->alias('mailer', Illuminate\Mail\Mailer::class);
    $app->alias('mailer', Illuminate\Contracts\Mail\Mailer::class);
    $app->alias('mailer', Illuminate\Contracts\Mail\MailQueue::class);

The following configuration options should also be available to your Lumen application's `.env` file:

    MAIL_MAILER=smtp
    MAIL_HOST=smtp.mailtrap.io
    MAIL_PORT=2525
    MAIL_USERNAME=
    MAIL_PASSWORD=
    MAIL_ENCRYPTION=tls
    MAIL_FROM_ADDRESS=hello@example.com
    MAIL_FROM_NAME="Example app"

## Usage

The Lumen mail drivers utilize the same code as the Laravel mail drivers. Beyond configuration, there are no differences between sending mail in Lumen and sending mail in Laravel; therefore, please consult the [full Laravel documentation](https://laravel.com/docs/mail) for usage examples.
