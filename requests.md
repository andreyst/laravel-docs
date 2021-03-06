# HTTP Requests

- [Accessing The Request](#accessing-the-request)
- [Retrieving Input](#retrieving-input)
- [Old Input](#old-input)
- [Cookies](#cookies)
- [Files](#files)
- [Other Request Information](#other-request-information)

<a name="accessing-the-request"></a>
## Accessing The Request

### Via Dependency Injection

To obtain an instance of the current HTTP request via dependency injection, you should type-hint the class on your controller constructor or method. The current request instance will automatically be injected by the [service container](/docs/{{version}}/container):

	<?php namespace App\Http\Controllers;

	use Illuminate\Http\Request;
	use Illuminate\Routing\Controller;

	class UserController extends Controller
	{
		/**
		 * Store a new user.
		 *
		 * @param  Request  $request
		 * @return Response
		 */
		public function store(Request $request)
		{
			$name = $request->input('name');

			//
		}
	}

If your controller method is also expecting input from a route parameter, simply list your route arguments after your other dependencies. For example, if your route is defined like so:

	Route::put('user/{id}', 'UserController@update');

You may stil type-hint the `Request` and access your route parameter `id` by defining your controller method like the following:

	<?php namespace App\Http\Controllers;

	use Illuminate\Http\Request;
	use Illuminate\Routing\Controller;

	class UserController extends Controller
	{
		/**
		 * Store a new user.
		 *
		 * @param  Request  $request
		 * @param  int  $id
		 * @return Response
		 */
		public function update(Request $request, $id)
		{
			//
		}
	}

<a name="retrieving-input"></a>
## Retrieving Input

#### Retrieving An Input Value

Using a few simple methods, you may access all user input from your `Illuminate\Http\Request` instance. You do not need to worry about the HTTP verb used for the request, as input is accessed in the same way for all verbs.

	$name = $request->input('name');

#### Retrieving A Default Value If The Input Value Is Absent

	$name = $request->input('name', 'Sally');

#### Determining If An Input Value Is Present

	if ($request->has('name'))
	{
		//
	}

#### Getting All Input For The Request

	$input = $request->all();

#### Getting Only Some Of The Request Input

	$input = $request->only('username', 'password');

	$input = $request->except('credit_card');

When working on forms with "array" inputs, you may use dot notation to access the arrays:

	$input = $request->input('products.0.name');

<a name="old-input"></a>
## Old Input

Laravel also allows you to keep input from one request during the next request. For example, you may need to re-populate a form after checking it for validation errors.

#### Flashing Input To The Session

The `flash` method will flash the current input to the [session](/docs/{{version}}/session) so that it is available during the user's next request to the application:

	$request->flash();

#### Flashing Only Some Input To The Session

	$request->flashOnly('username', 'email');

	$request->flashExcept('password');

#### Flash & Redirect

Since you often will want to flash input in association with a redirect to the previous page, you may easily chain input flashing onto a redirect.

	return redirect('form')->withInput();

	return redirect('form')->withInput($request->except('password'));

#### Retrieving Old Data

To retrieve flashed input from the previous request, use the `old` method on the `Request` instance.

	$username = $request->old('username');

If you are displaying old input within a Blade template, it is more convenient to use the `old` helper:

	{{ old('username') }}

<a name="cookies"></a>
## Cookies

All cookies created by the Laravel framework are encrypted and signed with an authentication code, meaning they will be considered invalid if they have been changed by the client.

#### Retrieving A Cookie Value

	$value = $request->cookie('name');

#### Attaching A New Cookie To A Response

The `cookie` helper serves as a simple factory for generating new `Symfony\Component\HttpFoundation\Cookie` instances. The cookies may be attached to a `Response` instance using the `withCookie` method:

	$response = new Illuminate\Http\Response('Hello World');

	$response->withCookie(cookie('name', 'value', $minutes));

#### Creating A Cookie That Lasts Forever*

_By "forever", we really mean five years._

	$response->withCookie(cookie()->forever('name', 'value'));

<a name="files"></a>
## Files

#### Retrieving An Uploaded File

	$file = $request->file('photo');

#### Determining If A File Was Uploaded

	if ($request->hasFile('photo'))
	{
		//
	}

The object returned by the `file` method is an instance of the `Symfony\Component\HttpFoundation\File\UploadedFile` class, which extends the PHP `SplFileInfo` class and provides a variety of methods for interacting with the file.

#### Determining If An Uploaded File Is Valid

	if ($request->file('photo')->isValid())
	{
		//
	}

#### Moving An Uploaded File

	$request->file('photo')->move($destinationPath);

	$request->file('photo')->move($destinationPath, $fileName);

### Other File Methods

There are a variety of other methods available on `UploadedFile` instances. Check out the [API documentation for the class](http://api.symfony.com/2.5/Symfony/Component/HttpFoundation/File/UploadedFile.html) for more information regarding these methods.

<a name="other-request-information"></a>
## Other Request Information

The `Request` class provides many methods for examining the HTTP request for your application and extends the `Symfony\Component\HttpFoundation\Request` class. Here are some of the highlights.

#### Retrieving The Request URI

	$uri = $request->path();

#### Retrieving The Request Method

	$method = $request->method();

	if ($request->isMethod('post'))
	{
		//
	}

#### Determining If The Request Path Matches A Pattern

	if ($request->is('admin/*'))
	{
		//
	}

#### Get The Current Request URL

	$url = $request->url();
