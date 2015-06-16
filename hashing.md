# Hashing

- [Introduction](#introduction)
- [Basic Usage](#basic-usage)

<a name="introduction"></a>
## Introduction

The Lumen `Hash` facade provides secure Bcrypt hashing for storing user passwords. Bcrypt is a great choice for hashing passwords because its "work factor" is adjustable, which means that the time it takes to generate a hash can be increased as hardware power increases.

<a name="basic-usage"></a>
## Basic Usage

You may hash a password by calling the `make` method on the `Hash` facade:

	<?php

	namespace App\Http\Controllers;

	use Hash;
	use App\User;
	use Illuminate\Http\Request;
	use App\Http\Controllers\Controller;

	class UserController extends Controller
	{
		/**
		 * Update the password for the user.
		 *
		 * @param  Request  $request
		 * @param  int  $id
		 * @return Response
		 */
		public function updatePassword(Request $request, $id)
		{
			$user = User::findOrFail($id);

			// Validate the new password length...

			$user->fill([
				'password' => Hash::make($request->newPassword)
			])->save();
		}
	}

Alternatively, you may also use the global `bcrypt` helper function:

	bcrypt('plain-text');

#### Verifying A Password Against A Hash

The `check` method allows you to verify that a given plain-text string corresponds to a given hash:

	if (Hash::check('plain-text', $hashedPassword)) {
		// The passwords match...
	}

#### Checking If A Password Needs To Be Rehashed

The `needsRehash` function allows you to determine if the work factor used by the hasher has changed since the password was hashed:

	if (Hash::needsRehash($hashed)) {
		$hashed = Hash::make('plain-text');
	}
