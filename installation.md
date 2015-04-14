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
