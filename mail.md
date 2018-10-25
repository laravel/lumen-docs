# Mail

## Introduction

Lumen provides a clean, simple API over the popular [SwiftMailer](https://swiftmailer.symfony.com/) library with drivers for SMTP, Mailgun, SparkPost, Amazon SES, PHP's `mail` function, and `sendmail`, allowing you to quickly get started sending mail through a local or cloud based service of your choice.

### Configuration

Before using Laravel mail drivers in Lumen, you will need to install the `illuminate/mail` package via Composer. Then, you should register the `Illuminate\Mail\MailServiceProvider` in your `bootstrap/app.php` file:

    $app->register(Illuminate\Mail\MailServiceProvider::class);
Then, you need to configure mail options by copying the entire `mail.php` file from the [full Laravel framework](https://github.com/laravel/laravel/blob/master/config/mail.php) to the `config` directory in the root of your project and adjust the necessary configuration options as needed. If the `config` directory does not exist, then you should create it.

After that, you should let Lumen take this file in account by adding:

    $app->configure('mail');
into your `bootstrap/app.php` file.

The following configuration options will also be available in the `.env` file:

    MAIL_DRIVER=smtp
    MAIL_HOST=smtp.mailtrap.io
    MAIL_PORT=2525
    MAIL_USERNAME=
    MAIL_PASSWORD=
    MAIL_ENCRYPTION=tls
    MAIL_FROM_ADDRESS=hello@example.com
    MAIL_FROM_NAME="Example app"

## Usage

The Lumen mail drivers utilize the exact same code as the full Laravel mail drivers. Beyond configuration, there are no differences between using the mail in Lumen and using the mail in Laravel; therefore, please consult the [full Laravel documentation](https://laravel.com/docs/mail) for usage examples.
