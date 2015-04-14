# Pagination

- [Configuration](#configuration)
- [Usage](#usage)
- [Appending To Pagination URLs](#appending-to-pagination-urls)
- [Converting To JSON](#converting-to-json)

<a name="configuration"></a>
## Configuration

In other frameworks, pagination can be very painful. Lumen and Laravel makes it a breeze. Lumen can generate an intelligent "range" of links based on the current page. The generated HTML is compatible with the Bootstrap CSS framework.

Of course, if you are building a JSON API, the paginator will generate a useful JSON response, including URLs to the previous and next "page".

<a name="usage"></a>
## Usage

There are several ways to paginate items. The simplest is by using the `paginate` method on the query builder or an Eloquent model.

#### Paginating Database Results

	$users = DB::table('users')->paginate(15);

> **Note:** Currently, pagination operations that use a `groupBy` statement cannot be executed efficiently by Lumen and Laravel. If you need to use a `groupBy` with a paginated result set, it is recommended that you query the database and create a paginator manually.

#### Creating A Paginator Manually

Sometimes you may wish to create a pagination instance manually, passing it an array of items. You may do so by creating either an `Illuminate\Pagination\Paginator` or `Illuminate\Pagination\LengthAwarePaginator` instance, depending on your needs.

#### Paginating An Eloquent Model

You may also paginate [Eloquent](/docs/master/eloquent) models:

	$allUsers = User::paginate(15);

	$someUsers = User::where('votes', '>', 100)->paginate(15);

The argument passed to the `paginate` method is the number of items you wish to display per page. Once you have retrieved the results, you may simply return them from your route / controller. The results will automatically be converted to JSON.

You may also display the results in your view. Create the pagination links using the `render` method:

	<div class="container">
		@foreach ($users as $user)
			{{ $user->name }}
		@endforeach
	</div>

	{!! $users->render() !!}

You may also access additional pagination information via the following methods on the paginator:

- `currentPage`
- `lastPage`
- `perPage`
- `hasMorePages`
- `url`
- `nextPageUrl`
- `total`
- `count`

#### "Simple Pagination"

If you do not need to inform the user how many "total" items you are paginating, you have the option of using the `simplePaginate` method to perform a more efficient query. This is useful for larger datasets:

	$someUsers = User::where('votes', '>', 100)->simplePaginate(15);

#### Customizing The Paginator URI

You may also customize the URI used by the paginator via the `setPath` method:

	$users = User::paginate();

	$users->setPath('custom/url');

The example above will create URLs like the following: http://example.com/custom/url?page=2

<a name="appending-to-pagination-urls"></a>
## Appending To Pagination URLs

You can add to the query string of pagination links using the `appends` method on the Paginator:

	$users->appends(['sort' => 'votes']);

This will generate URLs that look something like this:

	http://example.com/something?page=2&sort=votes

If you wish to append a "hash fragment" to the paginator's URLs, you may use the `fragment` method:

	$users->fragment('foo');

This method call will generate URLs that look something like this:

	http://example.com/something?page=2#foo

<a name="converting-to-json"></a>
## Converting To JSON

The `Paginator` class implements the `Illuminate\Contracts\Support\JsonableInterface` contract and exposes the `toJson` method. You may also convert a `Paginator` instance to JSON by returning it from a route. The JSON'd form of the instance will include some "meta" information such as `total`, `current_page`, and `last_page`. The instance's data will be available via the `data` key in the JSON array.
