# Hashing

- [Introduction](#introduction)
- [Basic Usage](#basic-usage)

<a name="introduction"></a>
## Introduction

The Lumen `Hash` facade provides secure Bcrypt hashing for storing user passwords.

<a name="basic-usage"></a>
## Basic Usage

> **Note:** If you intend to use the `Hash` facade, be sure to uncomment the `$app->withFacades()` call in your `bootstrap/app.php` file.

#### Hashing A Password Using Bcrypt

	$password = Hash::make('secret');

You may also use the `bcrypt` helper function:

	$password = bcrypt('secret');

#### Verifying A Password Against A Hash

	if (Hash::check('secret', $hashedPassword))	{
		// The passwords match...
	}

#### Checking If A Password Needs To Be Rehashed

	if (Hash::needsRehash($hashed))	{
		$hashed = Hash::make('secret');
	}
