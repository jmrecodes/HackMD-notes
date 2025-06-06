# Laravel Basics: Middleware

[![hackmd-github-sync-badge](https://hackmd.io/@jmrecodes/HyoOGtJXel/badge)](https://hackmd.io/@jmrecodes/HyoOGtJXel)

Middleware in Laravel provide a convenient mechanism for inspecting and filtering HTTP requests entering your application. For example, Laravel includes middleware that verifies the user of your application is authenticated. If the user is not authenticated, the middleware will redirect the user to your application's login screen. However, if the user is authenticated, the middleware will allow the request to proceed further into the application.

## What is Middleware?

*   **HTTP Request Filtering:** Middleware act as layers that HTTP requests must pass through before they reach a route or controller.
*   **Multiple Purposes:** They can be used for various tasks like authentication, authorization, logging, CORS handling, modifying request/response headers, etc.
*   **Chainable:** Multiple middleware can be applied to a route, forming a chain that the request traverses.

## Creating Middleware

You can generate new middleware using the Artisan command:

```bash
php artisan make:middleware CheckAge
```
This command will place a new `CheckAge` class within your `app/Http/Middleware` directory.

In this middleware, we will only allow access to the route if the supplied `age` is greater than 200. Otherwise, we will redirect the users back to the `home` URI.

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class CheckAge
{
    /**
     * Handle an incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure(\Illuminate\Http\Request): (\Illuminate\Http\Response|\Illuminate\Http\RedirectResponse)  $next
     * @return \Illuminate\Http\Response|\Illuminate\Http\RedirectResponse
     */
    public function handle(Request $request, Closure $next)
    {
        if ($request->input('age') <= 200) {
            return redirect('home');
        }

        // If the age is sufficient, pass the request to the next middleware/route handler
        return $next($request);
    }
}
```
As you can see, if the given `age` is less than or equal to `200`, the middleware will return an HTTP redirect to the client; otherwise, the request will be passed further into the application. To pass the request deeper into the application (allowing the middleware to "pass"), call the `$next` callback with the `$request`.

## Registering Middleware

For a middleware to be active, it needs to be registered.

### Global Middleware
If you want a middleware to run during every HTTP request to your application, list the middleware class in the `$middleware` property of your `app/Http/Kernel.php` class.

### Assigning Middleware to Routes (Route Middleware)
If you would like to assign middleware to specific routes, you should first assign the middleware a key in your application's `app/Http/Kernel.php` file. By default, the `$routeMiddleware` property of this class contains entries for the middleware included with Laravel. To add your own, append it to this list and assign it a key of your choosing:

```php
// Within App\Http\Kernel class...

protected $routeMiddleware = [
    'auth' => \App\Http\Middleware\Authenticate::class,
    'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
    'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
    'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
    // Add your custom middleware
    'check.age' => \App\Http\Middleware\CheckAge::class,
];
```

Once the middleware has been defined in the HTTP kernel, you may use the `middleware` method to assign middleware to a route:

```php
Route::get('/admin/profile', function () {
    //
})->middleware('check.age');

// Assigning multiple middleware
Route::get('/', function () {
    //
})->middleware(['first', 'second']);
```

You can also assign middleware within route groups:
```php
Route::middleware(['check.age'])->group(function () {
    Route::get('/settings', function () {
        // ...
    });
    Route::get('/profile', function () {
        // ...
    });
});
```

## Middleware Parameters

Middleware can also receive additional parameters. For example, if your application needs to verify that the authenticated user has a specific "role" before performing a given action, you could create a `CheckRole` middleware that receives a role name as an additional argument.

Additional middleware parameters will be passed to the middleware after the `$next` argument:

```php
<?php

namespace App\Http\Middleware;

use Closure;

class CheckRole
{
    /**
     * Handle the incoming request.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @param  string  $role
     * @return mixed
     */
    public function handle($request, Closure $next, $role)
    {
        if (! $request->user()->hasRole($role)) {
            // Redirect or abort...
        }

        return $next($request);
    }
}
```

Middleware parameters may be specified when defining the route by separating the middleware name and parameters with a `:` character. Multiple parameters should be comma-delimited:

```php
Route::put('/post/{id}', function ($id) {
    //
})->middleware('role:editor');
```

---
[Back to Laravel Basics Index](laravel-basics-index.md)

[Back to Knowledge Base Index](/README.md)