# Laravel Basics: Controllers

[![hackmd-github-sync-badge](https://hackmd.io/@jmrecodes/SyTzMFkQlg/badge)](https://hackmd.io/@jmrecodes/SyTzMFkQlg)

Controllers are a fundamental part of Laravel's MVC (Model-View-Controller) architecture. Instead of defining all request handling logic as closures in your route files, you can organize this behavior using controller classes.

## What is a Controller?

*   **Organization:** Controllers group related HTTP request handling logic into a single class. For example, a `UserController` might handle all incoming requests related to users, including showing a list of users, creating a new user, updating a user, etc.
*   **Location:** Controllers are stored in the `app/Http/Controllers` directory.

## Creating Controllers

You can generate a new controller using the Artisan command-line interface:

```bash
php artisan make:controller PhotoController
```

This will create a `PhotoController.php` file in `app/Http/Controllers`.

A basic controller might look like this:

```php
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller; // Base Controller
use Illuminate\Http\Request; // To handle HTTP requests
use App\Models\User; // Example Model

class UserController extends Controller
{
    /**
     * Show a list of all users.
     */
    public function index()
    {
        $users = User::all();
        return view('users.index', ['users' => $users]);
    }

    /**
     * Show the form for creating a new user.
     */
    public function create()
    {
        return view('users.create');
    }

    /**
     * Store a newly created user in storage.
     */
    public function store(Request $request)
    {
        // Validate the request...
        $validated = $request->validate([
            'name' => 'required|string|max:255',
            'email' => 'required|string|email|max:255|unique:users',
            'password' => 'required|string|min:8',
        ]);

        $user = User::create($validated);

        return redirect()->route('users.show', ['user' => $user->id]);
    }

    /**
     * Display the specified user.
     */
    public function show(string $id)
    {
        $user = User::findOrFail($id);
        return view('users.show', ['user' => $user]);
    }

    // ... other methods like edit, update, destroy
}
```

## Controller Methods and Routing

Controller methods are typically mapped to routes in your `routes/web.php` or `routes/api.php` files:

```php
use App\Http\Controllers\UserController;

Route::get('/users', [UserController::class, 'index']);
Route::get('/users/create', [UserController::class, 'create']);
Route::post('/users', [UserController::class, 'store']);
Route::get('/users/{id}', [UserController::class, 'show']);
```

## Resource Controllers

For typical CRUD (Create, Read, Update, Delete) operations, Laravel provides resource controllers. You can generate a resource controller with all the standard CRUD methods using Artisan:

```bash
php artisan make:controller PhotoController --resource
```

Then, define a single route in your routes file:

```php
use App\Http\Controllers\PhotoController;

Route::resource('photos', PhotoController::class);
```

This single line will create routes for the following actions:
*   `GET /photos` (index)
*   `GET /photos/create` (create)
*   `POST /photos` (store)
*   `GET /photos/{photo}` (show)
*   `GET /photos/{photo}/edit` (edit)
*   `PUT/PATCH /photos/{photo}` (update)
*   `DELETE /photos/{photo}` (destroy)

## Dependency Injection in Controllers

Laravel's service container is used to resolve all Laravel controllers. As a result, you can type-hint any dependencies your controller may need in its constructor or methods. The dependencies will automatically be resolved and injected.

```php
<?php

namespace App\Http\Controllers;

use App\Repositories\UserRepository; // Example service
use Illuminate\Http\Request;

class UserController extends Controller
{
    protected $users;

    // Constructor Injection
    public function __construct(UserRepository $users)
    {
        $this->users = $users;
    }

    public function store(Request $request) // Method Injection for Request
    {
        // Use $request object
        // Use $this->users repository
    }
}
```

---
[Back to Laravel Basics Index](laravel-basics-index.md)

[Back to Knowledge Base Index](/README.md)