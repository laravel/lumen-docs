# Views

- [Basic Usage](#basic-usage)

<a name="basic-usage"></a>
## Basic Usage

Views contain the HTML served by your application, and serve as a convenient method of separating your controller and domain logic from your presentation logic. Views are stored in the `resources/views` directory.

A simple view looks like this:

	<!-- View stored in resources/views/greeting.php -->

	<!doctype html>
	<html>
		<head>
			<title>Welcome!</title>
		</head>
		<body>
			<h1>Hello, <?php echo $name; ?></h1>
		</body>
	</html>

The view may be returned to the browser like so:

	$app->get('/', function() {
		return view('greeting', ['name' => 'James']);
	});

As you can see, the first argument passed to the `view` helper corresponds to the name of the view file in the `resources/views` directory. The second argument passed to helper is an array of data that should be made available to the view.

Of course, views may also be nested within sub-directories of the `resources/views` directory. For example, if your view is stored at `resources/views/admin/profile.php`, it should be returned like so:

	return view('admin.profile', $data);

#### Passing Data To Views

	// Using conventional approach
	$view = view('greeting')->with('name', 'Victoria');

	// Using Magic Methods
	$view = view('greeting')->withName('Victoria');

In the example above, the variable `$name` is made accessible to the view and contains `Victoria`.

If you wish, you may pass an array of data as the second parameter to the `view` helper:

	$view = view('greetings', $data);

When passing information in this manner, `$data` should be an array with key/value pairs. Inside your view, you can then access each value using it's corresponding key, like `{{ $key }}` (assuming `$data['$key']` exists).

#### Determining If A View Exists

If you need to determine if a view exists, you may use the `exists` method:

	if (view()->exists('emails.customer')) {
		//
	}

#### Returning A View From A File Path

If you wish, you may generate a view from a fully-qualified file path:

	return view()->file($pathToFile, $data);
