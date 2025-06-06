# Laravel Basics: Requests, Responses, Forms & Validation

[![hackmd-github-sync-badge](https://hackmd.io/@jmrecodes/HkHnfF1mxe/badge)](https://hackmd.io/@jmrecodes/HkHnfF1mxe)

Interacting with incoming HTTP requests, sending appropriate responses, handling HTML forms, and validating user input are core aspects of any web application. Laravel provides elegant and robust ways to manage these tasks.

## HTTP Requests (`Illuminate\Http\Request`)

Laravel encapsulates the incoming HTTP request into an `Illuminate\Http\Request` object. This object provides methods to examine the HTTP request (its URI, method, headers, input data, uploaded files, etc.).

### Accessing the Request
You can type-hint the `Illuminate\Http\Request` class on your controller methods or route closures. Laravel's service container will automatically inject the current request instance:

```php
use Illuminate\Http\Request;

Route::post('/user', function (Request $request) {
    $name = $request->input('name');
    // ...
});

// In a controller
public function store(Request $request)
{
    $name = $request->input('name');
    $email = $request->email; // Dynamic property access
    // ...
}
```

### Retrieving Input
*   `$request->input('key', 'default_value')`: Retrieves a specific input item.
*   `$request->all()`: Retrieves all input data as an array.
*   `$request->query('key', 'default')`: Retrieves input from the query string.
*   `$request->has('key')`: Checks if an input item is present.
*   `$request->filled('key')`: Checks if an input item is present and not empty.
*   `$request->method()`: Gets the HTTP verb (GET, POST, etc.).
*   `$request->path()`: Gets the request path.
*   `$request->url()`: Gets the full URL.
*   `$request->header('X-Header-Name')`: Retrieves a request header.
*   `$request->file('photo')`: Retrieves an uploaded file.

## HTTP Responses

Laravel provides several ways to return HTTP responses.

### Basic Responses
*   **Strings & Arrays:** Returning a string or array from a route or controller will automatically be converted into an HTTP response (arrays become JSON).
    ```php
    Route::get('/', function () {
        return 'Hello World';
    });
    Route::get('/data', function () {
        return ['name' => 'Abigail', 'state' => 'CA']; // Becomes JSON
    });
    ```
*   **Response Objects:** For more control, you can return a full `Illuminate\Http\Response` instance or use the `response()` helper:
    ```php
    use Illuminate\Http\Response;

    Route::get('/home', function () {
        return response('Hello World', 200)
                      ->header('Content-Type', 'text/plain');
    });
    ```

### View Responses
Typically, you'll return a Blade view:
```php
return view('greeting', ['name' => 'James']);
```

### JSON Responses
```php
return response()->json([
    'name' => 'Abigail',
    'state' => 'CA'
]);
```

### Redirects
*   `redirect('/home/dashboard')`
*   `redirect()->route('profile', ['id' => 1])` (redirect to a named route)
*   `redirect()->back()` (redirect to the previous location)
*   `redirect()->back()->with('status', 'Profile updated!')` (redirect with flashed session data)

## HTML Forms

When defining an HTML form in your Blade views, remember to include a CSRF token field to protect against cross-site request forgery attacks. Laravel makes this easy with the `@csrf` Blade directive:

```blade
<form method="POST" action="/profile">
    @csrf
    <!-- ... other form fields ... -->
    <button type="submit">Submit</button>
</form>
```
For `PUT`, `PATCH`, or `DELETE` requests submitted via HTML forms (which only support GET and POST natively), you'll need to use the `@method` Blade directive to spoof the HTTP verb:

```blade
<form method="POST" action="/profile/{{ $user->id }}">
    @csrf
    @method('PUT')
    <!-- ... -->
</form>
```

## Validation

Laravel provides several approaches to validate your application's incoming data.

### Basic Validation in Controllers
The `validate` method on the `Illuminate\Http\Request` object is convenient:

```php
public function store(Request $request)
{
    $validated = $request->validate([
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
        'publish_at' => 'nullable|date',
    ]);

    // The blog post is valid...
    // $validated contains only the validated data.
    Post::create($validated);
}
```
If the validation rules pass, your code will continue executing normally. If validation fails, an `Illuminate\Validation\ValidationException` exception will be thrown, and the proper HTTP response (often a redirect back with error messages flashed to the session) will automatically be sent back to the user.

### Displaying Validation Errors
In your Blade view, you can use the automatically available `$errors` variable (an instance of `Illuminate\Support\MessageBag`) to display validation errors:

```blade
@if ($errors->any())
    <div class="alert alert-danger">
        <ul>
            @foreach ($errors->all() as $error)
                <li>{{ $error }}</li>
            @endforeach
        </ul>
    </div>
@endif

<!-- Displaying a specific field's first error -->
<input type="text" name="title">
@error('title')
    <div class="alert alert-danger">{{ $message }}</div>
@enderror
```

### Form Request Validation
For more complex validation scenarios, you can create "form request" classes. These are custom request classes that encapsulate their own validation and authorization logic.

Generate a form request using Artisan:
`php artisan make:request StorePostRequest`

This will create a class in `app/Http/Requests/StorePostRequest.php`:
```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class StorePostRequest extends FormRequest
{
    /**
     * Determine if the user is authorized to make this request.
     */
    public function authorize(): bool
    {
        // return false; // Example: only allow admins
        return true; // Typically true if validation is the main concern here
    }

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array<string, \Illuminate\Contracts\Validation\ValidationRule|array<mixed>|string>
     */
    public function rules(): array
    {
        return [
            'title' => 'required|unique:posts|max:255',
            'body' => 'required',
        ];
    }

    /**
     * Get custom messages for validator errors.
     */
    public function messages(): array
    {
        return [
            'title.required' => 'A title is absolutely required!',
            'body.required' => 'A message is required',
        ];
    }
}
```
Then, type-hint this custom request in your controller method:
```php
use App\Http\Requests\StorePostRequest;

public function store(StorePostRequest $request)
{
    // The incoming request is valid...
    // Retrieve the validated input data...
    $validated = $request->validated();

    Post::create($validated);
}
```
If validation fails, Laravel automatically redirects back. If the `authorize` method returns `false`, a 403 HTTP response is automatically returned.

---
[Back to Laravel Basics Index](laravel-basics-index.md)

[Back to Knowledge Base Index](/README.md)