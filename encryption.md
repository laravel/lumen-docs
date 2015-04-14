# Encryption

- [Introduction](#introduction)
- [Basic Usage](#basic-usage)

<a name="introduction"></a>
## Introduction

Laravel provides facilities for strong AES encryption via the Mcrypt PHP extension.

<a name="basic-usage"></a>
## Basic Usage

#### Encrypting A Value

	$encrypted = Crypt::encrypt('secret');

> **Note:** Be sure to set a 32 character random string in the `APP_KEY` option of the `.env` file. Otherwise, encrypted values will not be secure.

#### Decrypting A Value

	$decrypted = Crypt::decrypt($encryptedValue);

#### Setting The Cipher & Mode

You may also set the cipher and mode used by the encrypter:

	Crypt::setMode('ctr');

	Crypt::setCipher($cipher);
