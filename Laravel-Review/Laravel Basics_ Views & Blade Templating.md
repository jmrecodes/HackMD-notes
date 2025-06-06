# Laravel Basics: Views & Blade Templating

Views in Laravel are responsible for presenting data to the user. They typically contain the HTML markup of your application, along with any necessary logic to display dynamic content. Laravel uses the Blade templating engine, which is powerful yet simple.

## What are Views?

*   **Presentation Layer:** Views separate your application's presentation logic from its business logic (Controllers and Models).
*   **HTML & Dynamic Data:** They render HTML and can display data passed from controllers.
*   **Location:** Views are stored in the `resources/views` directory.
*   **Naming Convention:** View files use the `.blade.php` extension (e.g., `welcome.blade.php`). This tells Laravel to process the file with the Blade templating engine.

## Creating Views

Simply create a new file with the `.blade.php` extension in the `resources/views` directory or a subdirectory. For example, `resources/views/posts/show.blade.php`.

## Passing Data to Views

Data is typically passed to views from your controllers using an associative array as the second argument to the `view` helper function:

```php
// In a Controller method
use App\Models\Post;

public function show(string $id)
{
    $post = Post::findOrFail($id);
    return view('posts.show', ['post' => $post, 'title' => 'My Post Title']);
}
```

Inside the `posts/show.blade.php` view, you can then access `$post` and `$title` as variables.

## Blade Templating Engine

Blade provides a clean and concise syntax for writing views.

### Displaying Data
To display the contents of a variable, wrap it in double curly braces:
```blade
<h1>{{ $post->title }}</h1>
<p>{{ $post->content }}</p>
```
Blade `{{ }}` statements are automatically sent through PHP's `htmlspecialchars` function to prevent XSS attacks. If you need to render unescaped HTML (use with caution), you can use the `{!! !!}` syntax:
```blade
<div>{!! $post->html_content !!}</div>
```

### Control Structures
Blade provides convenient directives for common PHP control structures:

**If Statements:**
```blade
@if (count($records) === 1)
    I have one record!
@elseif (count($records) > 1)
    I have multiple records!
@else
    I don't have any records!
@endif

@unless (Auth::check())
    You are not signed in.
@endunless

@isset($records)
    // $records is defined and not null...
@endisset

@empty($records)
    // $records is "empty"...
@endempty
```

**Loops:**
```blade
@foreach ($users as $user)
    <p>This is user {{ $user->id }}</p>
@endforeach

@forelse ($users as $user)
    <li>{{ $user->name }}</li>
@empty
    <p>No users</p>
@endforelse

@while (true)
    <p>I'm looping forever.</p>
@endwhile
```

### Template Inheritance (Layouts)
Blade allows you to define a master layout and then extend it in child views.

**Defining a Layout (`resources/views/layouts/app.blade.php`):**
```blade
<html>
    <head>
        <title>App Name - @yield('title')</title>
    </head>
    <body>
        @section('sidebar')
            This is the master sidebar.
        @show

        <div class="container">
            @yield('content')
        </div>
    </body>
</html>
```
*   `@yield('sectionName')`: Displays the content of a given section provided by a child view.
*   `@section('sectionName') ... @show`: Defines a section that can be overridden or appended to by child views. `@show` will display the default content if not overridden.

**Extending a Layout (`resources/views/greeting.blade.php`):**
```blade
@extends('layouts.app')

@section('title', 'Page Title')

@section('sidebar')
    @parent {{-- Appends to the master sidebar --}}
    <p>This is appended to the master sidebar.</p>
@endsection

@section('content')
    <p>This is my body content.</p>
@endsection
```

### Including Subviews
You can include other Blade views within a view using the `@include` directive:
```blade
<div>
    @include('shared.errors')
    <form>...</form>
</div>
```
Data can be passed to the included view: `@include('shared.card', ['item' => $widget])`

### Components & Slots
Laravel offers a powerful component system (similar to Vue/React components) for building reusable UI elements with Blade. This is a more advanced feature but very useful for larger applications.

---
[Back to Laravel Basics Index](https://hackmd.io/@jmrecodes/B1PTGFkXgl)