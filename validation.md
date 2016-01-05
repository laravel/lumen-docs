# Validation

- [Introduction](#introduction)
- [Validation Quickstart](#validation-quickstart)
- [Other Validation Approaches](#other-validation-approaches)
	- [Manually Creating Validators](#manually-creating-validators)
- [Working With Error Messages](#working-with-error-messages)
	- [Custom Error Messages](#custom-error-messages)
- [Available Validation Rules](#available-validation-rules)
- [Conditionally Adding Rules](#conditionally-adding-rules)
- [Custom Validation Rules](#custom-validation-rules)

<a name="introduction"></a>
## Introduction

Lumen provides several different approaches to validate your application's incoming data. By default, Lumen's base controller class uses a `ValidatesRequests` trait which provides a convenient method to validate incoming HTTP request with a variety of powerful validation rules.

**In general, validation in Lumen works exactly like validation in Laravel, so you should consult the [full Laravel validation documentation](https://laravel.com/docs/validation); however, there are a few important differences.**

## Differences From Laravel

#### Form Requests

Form requests are not supported by Lumen. If you would like to use form requests, you should use the full Laravel framework.

#### The `$this->validate` Method

The `$this->validate` helper which is available in Lumen will always return a JSON response with the relevant error messages. This is in contrast to the Laravel version of the method which will return a redirect response if the request is not an AJAX request. Since Lumen is stateless and does not support sessions, flashing errors to the session is not a possibility. **If you would like to used redirects and flashed error data, you should use the full Laravel framework.**

Unlike Laravel, Lumen provides access to the `validate` method from within Route closures:

	use Illuminate\Http\Request;

	$app->post('/user', function (Request $request) {
		$this->validate($request, [
			'name' => 'required',
			'email' => 'required|email|unique:users'
		]);

		// Store User...
	});

Of course, you are free to manually create validator instances using the `Validator::make` facade method just as you would in Laravel.

#### The `$errors` View Variable

Lumen does not support sessions or views out of the box, so the `$errors` view variable that is available in every view in Laravel is not available in Lumen. The `$this->validate` helper will **always** return JSON responses with the relevant error messages when validation fails. **If you are not building a stateless API that solely sends JSON responses, you should use the full Laravel framework.**
