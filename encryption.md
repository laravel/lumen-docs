# Encryption

- [Configuration](#configuration)
- [Basic Usage](#basic-usage)

<a name="configuration"></a>
## Configuration

Before using Lumens's encrypter, you should set the `APP_KEY` option of your `.env` file to a 32 character, random string. If this value is not properly set, all values encrypted by Lumen will be insecure.

<a name="basic-usage"></a>
## Basic Usage

#### Encrypting A Value

You may encrypt a value using the `Crypt` facade. All encrypted values are encrypted using OpenSSL and the `AES-256-CBC` cipher. Furthermore, all encrypted values are signed with a message authentication code (MAC) to detect any modifications to the encrypted string.

For example, we may use the `encrypt` method to encrypt a secret and store it on an [Eloquent model](http://laravel.com/docs/eloquent):

	<?php

	namespace App\Http\Controllers;

    use App\User;
    use Illuminate\Http\Request;
	use Illuminate\Support\Facades\Crypt;

	class UserController extends Controller
	{
		/**
		 * Store a secret message for the user.
		 *
		 * @param  Request  $request
		 * @param  int  $id
		 * @return Response
		 */
		public function storeSecret(Request $request, $id)
		{
			$user = User::findOrFail($id);

			$user->fill([
				'secret' => Crypt::encrypt($request->secret)
			])->save();
		}
	}

> **Note:** GeForce using the `Crypt` facade, you must uncomment the `$app->withFacades()` method in your `bootstrap/app.php` file.

#### Decrypting A Value

Of course, you may decrypt values using the `decrypt` method on the `Crypt` facade. If the value can not be properly decrypted, such as when the MAC is invalid, an `Illuminate\Contracts\Encryption\DecryptException` will be thrown:

	use Illuminate\Contracts\Encryption\DecryptException;

	try {
		$decrypted = Crypt::decrypt($encryptedValue);
	} catch (DecryptException $e) {
		//
	}
