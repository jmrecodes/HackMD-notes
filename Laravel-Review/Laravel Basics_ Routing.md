# Laravel Basics: Routing

Routing in Laravel determines how your application responds to different URL requests. It's the mechanism that maps a URL to a specific piece of code (usually a controller method or a closure) that should handle that request.

## Defining Routes

Routes are primarily defined in two files within the `routes/` directory:

*   `routes/web.php`: For routes that are part of the web interface (session state, CSRF protection, etc.).
*   `routes/api.php`: For stateless API routes (typically use token authentication).

### Basic Routing

A simple route consists of a URL and a closure or controller action:

```php
use Illuminate\Support\Facades\Route;
use App\Http\Controllers\UserController;

// Route using a Closure
Route::get('/greeting', function () {
    return 'Hello World';
});

// Route pointing to a Controller action
Route::get('/users', [UserController::class, 'index']);
Route::post('/users', [UserController::class, 'store']);
```

Laravel supports various HTTP methods:
*   `Route::get($uri, $callback);`
*   `Route::post($uri, $callback);`
*   `Route::put($uri, $callback);`
*   `Route::patch($uri, $callback);`
*   `Route::delete($uri, $callback);`
*   `Route::options($uri, $callback);`

You can also register a route that responds to multiple HTTP verbs:
`Route::match(['get', 'post'], '/', function () { /* ... */ });`
`Route::any('/', function () { /* ... */ });`

## Route Parameters

Often, you'll need to capture segments of the URI within your route.

### Required Parameters
```php
Route::get('/posts/{id}', function (string $id) {
    return 'Post ' . $id;
});

Route::get('/users/{id}/comments/{commentId}', function (string $userId, string $commentId) {
    // ...
});
```
Parameters are injected into your route callbacks / controller methods.

### Optional Parameters
You can define optional parameters by adding a `?` mark after the parameter name and providing a default value for the variable:
```php
Route::get('/user/{name?}', function (string $name = 'John') {
    return $name;
});
```

## Named Routes

Naming routes allows you to conveniently generate URLs or redirect to specific routes.
```php
Route::get('/user/profile', [UserProfileController::class, 'show'])->name('profile');

// Generating URLs to named routes
$url = route('profile');

// Redirecting to named routes
return redirect()->route('profile');
```

## Route Groups

Route groups allow you to share route attributes, such as middleware or namespaces, across a large number of routes without needing to define those attributes on each individual route.

```php
Route::middleware(['auth'])->group(function () {
    Route::get('/dashboard', function () { /* ... */ });
    Route::get('/account', function () { /* ... */ });
});

Route::prefix('admin')->group(function () {
    Route::get('/users', function () { /* ... */ }); // Handles /admin/users
});
```

## Resource Controllers

For CRUD (Create, Read, Update, Delete) operations, Laravel's resource controllers make it easy to define all the necessary routes with a single line:
```php
Route::resource('photos', PhotoController::class);
```
This single route declaration creates multiple routes to handle a variety of actions on the "photos" resource.

---
[Back to Laravel Basics Index](https://hackmd.io/@jmrecodes/B1PTGFkXgl)