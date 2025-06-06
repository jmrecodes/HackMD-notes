# Laravel Basics: Models & Eloquent ORM

Laravel's Eloquent ORM (Object-Relational Mapper) provides a beautiful, simple ActiveRecord implementation for working with your database. Each database table has a corresponding "Model" which is used to interact with that table. Models allow you to query for data in your tables, as well as insert new records and update or delete existing ones.

## What is a Model?

*   **Representation:** A Model in Laravel typically represents a single database table.
*   **Interaction:** It provides an object-oriented way to interact with the data in that table.
*   **Location:** Models are typically stored in the `app/Models` directory.

## Creating Models

You can generate a new Eloquent model using the Artisan command:

```bash
php artisan make:model Post
```

This will create a `Post.php` file in `app/Models`. If you want to generate a database migration when you generate the model, you can use the `-m` or `--migration` option:

```bash
php artisan make:model Post -m
```

A basic model file looks like this:

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    use HasFactory; // Optional: for model factories used in testing/seeding

    // Table Name (if different from convention)
    // protected $table = 'my_posts';

    // Primary Key (if different from 'id')
    // protected $primaryKey = 'flight_id';

    // Timestamps (if you don't want created_at and updated_at)
    // public $timestamps = false;

    // Fillable attributes for mass assignment
    protected $fillable = ['title', 'content', 'user_id'];

    // Guarded attributes (opposite of $fillable)
    // protected $guarded = ['id'];
}
```

## Eloquent Conventions

*   **Table Names:** Eloquent assumes the database table is the plural, snake_case version of the model name. For example, a `User` model is assumed to map to a `users` table.
*   **Primary Keys:** Eloquent assumes the primary key for the table is named `id` and is an auto-incrementing integer.
*   **Timestamps:** Eloquent automatically expects `created_at` and `updated_at` columns on your tables. If you don't want these columns to be automatically managed, set the `$timestamps` public property on your model to `false`.

## Retrieving Data

Eloquent makes it easy to retrieve data:

```php
use App\Models\Post;

// Get all posts
$posts = Post::all();

// Find a post by its primary key
$post = Post::find(1);

// Find the first post matching constraints
$post = Post::where('is_published', true)->first();

// Find a post or throw a 404 exception if not found
$post = Post::findOrFail(1);

// Building queries
$activePosts = Post::where('status', 'active')
                   ->orderBy('title', 'desc')
                   ->take(10)
                   ->get();
```

## Inserting & Updating Data

### Creating Records (Mass Assignment)
You can create new records using the `create` method. First, ensure you've defined `fillable` or `guarded` properties on your model to protect against mass-assignment vulnerabilities.
```php
$post = Post::create([
    'title' => 'New Post Title',
    'content' => 'Amazing content here.',
    'user_id' => 1,
]);
```

### Updating Records
```php
$post = Post::find(1);
$post->title = 'Updated Title';
$post->save();

// Mass update
Post::where('is_published', false)->update(['is_published' => true]);
```

## Deleting Data
```php
$post = Post::find(1);
$post->delete();

// Delete by key
Post::destroy(1);
Post::destroy([1, 2, 3]); // Delete multiple

// Delete by query
Post::where('status', 'inactive')->delete();
```

## Eloquent Relationships
Eloquent excels at managing relationships between your database tables (e.g., one-to-one, one-to-many, many-to-many).
Example: A `User` can have many `Post`s. A `Post` belongs to a `User`.

In `User.php`:
```php
public function posts()
{
    return $this->hasMany(Post::class);
}
```
In `Post.php`:
```php
public function user()
{
    return $this->belongsTo(User::class);
}
```
Then you can easily access related models: `$user->posts` or `$post->user`.

---
[Back to Laravel Basics Index](https://hackmd.io/@jmrecodes/B1PTGFkXgl)