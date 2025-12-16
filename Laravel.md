# Complete Laravel Mastery Guide
## From Beginner to Expert - With Detailed Explanations

---

## Table of Contents
1. [Introduction & Setup](#introduction--setup)
2. [Routing](#routing)
3. [Controllers](#controllers)
4. [Views & Blade Templating](#views--blade-templating)
5. [Database & Migrations](#database--migrations)
6. [Eloquent ORM](#eloquent-orm)
7. [Relationships](#relationships)
8. [Authentication & Authorization](#authentication--authorization)
9. [Middleware](#middleware)
10. [Form Validation](#form-validation)
11. [File Storage](#file-storage)
12. [Queues & Jobs](#queues--jobs)
13. [Events & Listeners](#events--listeners)
14. [API Development](#api-development)
15. [Testing](#testing)
16. [Advanced Concepts](#advanced-concepts)
17. [Best Practices](#best-practices)

---

## 1. Introduction & Setup

### What is Laravel?
Laravel is a PHP web framework that makes building web applications easier and faster. Think of it as a collection of tools and best practices that handle common tasks like routing, database operations, authentication, and more. Instead of writing everything from scratch, Laravel provides ready-made solutions.

### Why Use Laravel?
- **Elegant Syntax**: Clean, readable code that's easy to understand
- **Built-in Features**: Authentication, routing, sessions, caching out of the box
- **Security**: Protection against SQL injection, XSS, CSRF attacks
- **Large Community**: Tons of tutorials, packages, and help available
- **Modern Tools**: Supports the latest PHP features and best practices

### Installation

```bash
# Method 1: Install Laravel via Composer (Package Manager for PHP)
composer create-project laravel/laravel my-app

# Method 2: Use Laravel Installer (Faster for multiple projects)
composer global require laravel/installer
laravel new my-app

# Navigate to your project
cd my-app

# Start development server (runs on http://localhost:8000)
php artisan serve
```

**Explanation**: 
- `composer` is like npm for PHP - it manages dependencies
- `my-app` is your project name (change it to whatever you want)
- `php artisan serve` starts a local development server so you can see your site in a browser

### Environment Configuration

```bash
# Copy the example environment file
cp .env.example .env

# Generate application key (important for security!)
php artisan key:generate
```

**Explanation of .env file**:
The `.env` file contains sensitive configuration like database passwords, API keys, etc. Never commit this file to Git!

```env
APP_NAME=Laravel
APP_ENV=local          # Environment: local, production, staging
APP_KEY=base64:...     # Encryption key (auto-generated)
APP_DEBUG=true         # Show errors (true in development, false in production)
APP_URL=http://localhost

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=my_database
DB_USERNAME=root
DB_PASSWORD=secret
```

---

## 2. Routing

### What is Routing?
Routing is like a GPS for your website. It tells Laravel: "When someone visits this URL, do this action." Every URL in your application needs a route defined.

**Think of it this way**: 
- URL: `/users` → Show list of users
- URL: `/users/5` → Show user with ID 5
- URL: `/posts/create` → Show form to create a post

### Basic Routes

```php
// routes/web.php

// Simple route - returns text directly
Route::get('/', function () {
    return 'Hello World!';
});

// Route that returns a view (HTML page)
Route::get('/welcome', function () {
    return view('welcome');  // looks for resources/views/welcome.blade.php
});

// Route with parameter (dynamic segment)
Route::get('/user/{id}', function ($id) {
    return "User ID: " . $id;
});
// When someone visits /user/5, $id will be 5
// When someone visits /user/100, $id will be 100
```

**HTTP Methods Explained**:
- `Route::get()` - Retrieve/display data (viewing pages)
- `Route::post()` - Submit data (like submitting a form)
- `Route::put()` - Update existing data completely
- `Route::patch()` - Update existing data partially
- `Route::delete()` - Delete data

```php
// Different HTTP methods
Route::get('/posts', function () {
    // Show all posts
});

Route::post('/posts', function () {
    // Create a new post (called when form is submitted)
});

Route::delete('/posts/{id}', function ($id) {
    // Delete post with given ID
});
```

### Route Parameters - More Examples

```php
// Optional parameter with default value
Route::get('/user/{name?}', function ($name = 'Guest') {
    return "Hello, " . $name;
});
// /user → "Hello, Guest"
// /user/John → "Hello, John"

// Route with constraints (validation)
Route::get('/user/{id}', function ($id) {
    return "User ID: " . $id;
})->where('id', '[0-9]+');  // Only numbers allowed
// /user/5 → Works ✓
// /user/abc → 404 Error ✗

// Multiple parameters
Route::get('/post/{category}/{slug}', function ($category, $slug) {
    return "Category: $category, Post: $slug";
});
// /post/technology/laravel-tips → Category: technology, Post: laravel-tips

// Multiple constraints
Route::get('/post/{slug}/{id}', function ($slug, $id) {
    return "Post: $slug - ID: $id";
})->where([
    'slug' => '[a-z-]+',     // Only lowercase letters and dashes
    'id' => '[0-9]+'         // Only numbers
]);
```

**Why use constraints?**
They prevent invalid data from reaching your code. If someone tries `/user/hacker-attempt`, it fails immediately instead of causing errors in your application.

### Named Routes

```php
// Define a named route
Route::get('/profile', [ProfileController::class, 'show'])->name('profile.show');

// Why name routes? You can reference them by name instead of URL
// If you change the URL later, you only update it in one place!

// Generate URL from named route in controller
$url = route('profile.show');  // Returns: http://yoursite.com/profile

// In Blade template
<a href="{{ route('profile.show') }}">View Profile</a>

// Redirect to named route
return redirect()->route('profile.show');

// Named route with parameters
Route::get('/user/{id}', [UserController::class, 'show'])->name('user.show');

// Generate URL with parameter
$url = route('user.show', ['id' => 5]);  // http://yoursite.com/user/5

// In Blade
<a href="{{ route('user.show', $user->id) }}">View User</a>
```

**Pro Tip**: Always use named routes! If you change `/profile` to `/my-profile` later, all your links automatically update.

### Route Groups

Route groups let you apply the same settings to multiple routes. This keeps your code DRY (Don't Repeat Yourself).

```php
// Prefix group - adds /admin to all routes inside
Route::prefix('admin')->group(function () {
    Route::get('/users', function () {
        // URL: /admin/users
    });
    Route::get('/posts', function () {
        // URL: /admin/posts
    });
});

// Middleware group - applies middleware to all routes
// (Middleware = code that runs before your route logic)
Route::middleware(['auth'])->group(function () {
    // These routes require user to be logged in
    Route::get('/dashboard', function () {
        // Only accessible if user is authenticated
    });
    Route::get('/profile', function () {
        // Only accessible if user is authenticated
    });
});

// Combined grouping - super powerful!
Route::prefix('admin')
    ->middleware('auth')           // Must be logged in
    ->name('admin.')              // All route names start with "admin."
    ->group(function () {
        Route::get('/dashboard', function () {
            // URL: /admin/dashboard
            // Name: admin.dashboard
            // Requires auth
        })->name('dashboard');
        
        Route::get('/users', function () {
            // URL: /admin/users
            // Name: admin.users
            // Requires auth
        })->name('users');
    });

// Access routes: route('admin.dashboard'), route('admin.users')
```

**Real-world example**: You have an admin panel with 20 routes. Instead of adding `->middleware('auth')` to each route, group them all together!

### Resource Routes - The Magic of Laravel

Resource routes are Laravel's way of creating all CRUD (Create, Read, Update, Delete) routes automatically.

```php
// One line creates 7 routes!
Route::resource('posts', PostController::class);
```

**This single line creates:**

| HTTP Method | URL | Controller Method | Purpose |
|------------|-----|------------------|---------|
| GET | /posts | index() | Show all posts |
| GET | /posts/create | create() | Show form to create post |
| POST | /posts | store() | Save new post to database |
| GET | /posts/{post} | show() | Show single post |
| GET | /posts/{post}/edit | edit() | Show form to edit post |
| PUT/PATCH | /posts/{post} | update() | Update post in database |
| DELETE | /posts/{post} | destroy() | Delete post from database |

**Explanation of each route**:
1. **index()**: Display a listing (like a blog homepage showing all posts)
2. **create()**: Show an HTML form to create new post
3. **store()**: Process the form submission and save to database
4. **show()**: Display a single post (like viewing a blog post)
5. **edit()**: Show an HTML form to edit existing post
6. **update()**: Process the edit form and update database
7. **destroy()**: Delete the post from database

```php
// Only want some routes? Use only()
Route::resource('posts', PostController::class)->only(['index', 'show']);
// Creates only index() and show() routes

// Want all except some? Use except()
Route::resource('posts', PostController::class)->except(['destroy']);
// Creates all routes except destroy()

// API resources (no create/edit forms needed)
Route::apiResource('posts', PostController::class);
// Creates index, store, show, update, destroy (skips create and edit)
```

**When to use Resource Routes**:
When you're building a standard CRUD feature (blog posts, products, users, etc.), resource routes save you tons of time and follow best practices automatically.

---

## 3. Controllers

### What is a Controller?
A controller is like a traffic cop. It receives requests, processes them (gets data, performs calculations), and returns responses. Controllers keep your route files clean by moving logic into organized classes.

**Without Controller** (Bad practice):
```php
Route::get('/users', function () {
    $users = DB::table('users')->get();
    return view('users', ['users' => $users]);
});
// Imagine having 50 routes like this in one file - messy!
```

**With Controller** (Good practice):
```php
Route::get('/users', [UserController::class, 'index']);
// Clean! Logic is organized in UserController.php
```

### Creating Controllers

```bash
# Basic controller
php artisan make:controller UserController

# Resource controller (with CRUD methods pre-built)
php artisan make:controller PostController --resource

# API controller (resource without create/edit methods)
php artisan make:controller ApiPostController --api

# Controller with model type-hinting
php artisan make:controller PostController --model=Post --resource
```

**What does this create?**
A new file in `app/Http/Controllers/` with a class and methods ready for you to fill in.

### Basic Controller Example

```php
// app/Http/Controllers/UserController.php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\User;

class UserController extends Controller
{
    // Method to show all users
    public function index()
    {
        // Get all users from database
        $users = User::all();
        
        // Pass $users to the view
        // compact('users') is shorthand for ['users' => $users]
        return view('users.index', compact('users'));
    }

    // Method to show single user
    public function show($id)
    {
        // Find user by ID, or throw 404 if not found
        $user = User::findOrFail($id);
        
        return view('users.show', compact('user'));
    }

    // Method to create a new user
    public function store(Request $request)
    {
        // Validate incoming data
        $validated = $request->validate([
            'name' => 'required|max:255',           // Must exist, max 255 chars
            'email' => 'required|email|unique:users', // Must be valid email and unique
            'password' => 'required|min:8'          // Must be at least 8 characters
        ]);

        // Create user in database
        $user = User::create([
            'name' => $validated['name'],
            'email' => $validated['email'],
            'password' => bcrypt($validated['password'])  // Encrypt password!
        ]);

        // Redirect to user's profile page with success message
        return redirect()->route('users.show', $user->id)
            ->with('success', 'User created successfully!');
    }
}
```

**Step-by-step explanation of store() method**:
1. **Validate**: Check if incoming data is correct (required fields exist, email is valid, etc.)
2. **Create**: Save validated data to database
3. **Redirect**: Send user to another page with a success message

**Why validate?**
- Prevents bad data from entering your database
- Protects against hackers trying to inject malicious data
- Gives users helpful error messages

### Complete Resource Controller Example

```php
// app/Http/Controllers/PostController.php

namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    /**
     * Display a listing of all posts
     * URL: GET /posts
     */
    public function index()
    {
        // Get all posts with their author (eager loading to avoid N+1 queries)
        // latest() orders by created_at DESC (newest first)
        // paginate(15) shows 15 posts per page
        $posts = Post::with('user')->latest()->paginate(15);
        
        return view('posts.index', compact('posts'));
    }

    /**
     * Show the form to create a new post
     * URL: GET /posts/create
     */
    public function create()
    {
        // Just show the form
        return view('posts.create');
    }

    /**
     * Store a newly created post in database
     * URL: POST /posts
     */
    public function store(Request $request)
    {
        // Validate form data
        $validated = $request->validate([
            'title' => 'required|max:255',
            'content' => 'required',
            'image' => 'nullable|image|max:2048'  // Optional, must be image, max 2MB
        ]);

        // Create post and automatically assign to logged-in user
        // auth()->user() gets the currently logged-in user
        $post = auth()->user()->posts()->create($validated);

        // Redirect with success message
        return redirect()->route('posts.show', $post)
            ->with('success', 'Post created successfully!');
    }

    /**
     * Display a single post
     * URL: GET /posts/{post}
     * 
     * Note: Post $post uses Route Model Binding
     * Laravel automatically finds the post by ID
     */
    public function show(Post $post)
    {
        // $post is already loaded! No need for Post::find($id)
        return view('posts.show', compact('post'));
    }

    /**
     * Show the form to edit a post
     * URL: GET /posts/{post}/edit
     */
    public function edit(Post $post)
    {
        // Check if user is allowed to edit this post
        // This uses a Policy (covered later)
        $this->authorize('update', $post);
        
        return view('posts.edit', compact('post'));
    }

    /**
     * Update the post in database
     * URL: PUT/PATCH /posts/{post}
     */
    public function update(Request $request, Post $post)
    {
        // Check authorization
        $this->authorize('update', $post);

        // Validate
        $validated = $request->validate([
            'title' => 'required|max:255',
            'content' => 'required'
        ]);

        // Update the post
        $post->update($validated);

        return redirect()->route('posts.show', $post)
            ->with('success', 'Post updated successfully!');
    }

    /**
     * Delete the post from database
     * URL: DELETE /posts/{post}
     */
    public function destroy(Post $post)
    {
        // Check authorization
        $this->authorize('delete', $post);
        
        // Delete the post
        $post->delete();

        return redirect()->route('posts.index')
            ->with('success', 'Post deleted successfully!');
    }
}
```

**Key Concepts Explained**:

**Route Model Binding**: 
Instead of writing `$post = Post::find($id)`, Laravel does it automatically when you type-hint `Post $post` in your method. If the post doesn't exist, Laravel automatically returns a 404 error.

**Authorization**: 
`$this->authorize('update', $post)` checks if the current user is allowed to update this post. This keeps your authorization logic centralized.

**Flash Messages**: 
`->with('success', 'Message')` stores a message in the session for one request. Perfect for showing "Success!" messages after redirects.

### Dependency Injection

Dependency Injection sounds complex, but it's simple: Laravel automatically gives your controller the objects it needs.

```php
namespace App\Http\Controllers;

use App\Services\PaymentService;
use App\Repositories\OrderRepository;

class OrderController extends Controller
{
    protected $paymentService;
    protected $orderRepository;

    /**
     * Laravel sees these type-hints and automatically creates instances
     * You don't need to manually create them with 'new PaymentService()'
     */
    public function __construct(
        PaymentService $paymentService,
        OrderRepository $orderRepository
    ) {
        $this->paymentService = $paymentService;
        $this->orderRepository = $orderRepository;
    }

    public function processPayment($orderId)
    {
        // Now you can use these services
        $order = $this->orderRepository->find($orderId);
        $result = $this->paymentService->process($order);
        
        return response()->json($result);
    }
}
```

**Why is this useful?**
- **Testability**: Easy to replace real services with fake ones during testing
- **Flexibility**: Change implementation without changing controller code
- **Organization**: Business logic stays in service classes, controllers stay clean

**Real-world analogy**: Instead of a chef making their own knife, someone hands them a knife when they start work. The chef doesn't care who made the knife or how - they just use it.

---

## 4. Views & Blade Templating

### What is Blade?
Blade is Laravel's templating engine. It's like HTML but with superpowers - you can use PHP logic, loops, conditionals, and reusable components. All Blade files end with `.blade.php` and live in `resources/views/`.

**Regular PHP (ugly)**:
```php
<h1><?php echo $title; ?></h1>
<?php if($user->isAdmin()): ?>
    <p>Admin Panel</p>
<?php endif; ?>
```

**Blade (beautiful)**:
```php
<h1>{{ $title }}</h1>
@if($user->isAdmin())
    <p>Admin Panel</p>
@endif
```

### Basic Blade Syntax

```php
{{-- resources/views/welcome.blade.php --}}

{{-- This is a Blade comment (not visible in HTML source) --}}

{{-- Escaped output (SAFE - prevents XSS attacks) --}}
<h1>{{ $title }}</h1>
{{-- If $title = "<script>alert('hack')</script>", it displays as text, not executes --}}

{{-- Unescaped output (DANGEROUS - only use with trusted data!) --}}
<div>{!! $htmlContent !!}</div>
{{-- If $htmlContent has HTML tags, they will render --}}

{{-- Using PHP functions --}}
<p>{{ strtoupper($name) }}</p>
<p>{{ date('Y-m-d') }}</p>

{{-- Default values (if $name is null, show 'Guest') --}}
<p>{{ $name ?? 'Guest' }}</p>

{{-- Conditional statements --}}
@if($user->isAdmin())
    <p>Welcome, Admin!</p>
@elseif($user->isModerator())
    <p>Welcome, Moderator!</p>
@else
    <p>Welcome, User!</p>
@endif

{{-- Unless (opposite of if) --}}
@unless($user->subscribed())
    <p>Please subscribe to access premium content.</p>
@endunless
{{-- Equivalent to: @if(!$user->subscribed()) --}}

{{-- Authentication checks --}}
@auth
    <p>You are logged in as {{ auth()->user()->name }}</p>
    <form action="{{ route('logout') }}" method="POST">
        @csrf
        <button>Logout</button>
    </form>
@endauth

@guest
    <p>Please <a href="{{ route('login') }}">log in</a></p>
@endguest

{{-- Check specific guard --}}
@auth('admin')
    <p>You are logged in as admin</p>
@endauth
```

**Security Note**: Always use `{{ }}` for user input. Only use `{!! !!}` for content you control (like content from a rich text editor that you've sanitized).

### Loops in Blade

```php
{{-- Foreach loop - most common --}}
@foreach($users as $user)
    <div class="user">
        <h3>{{ $user->name }}</h3>
        <p>{{ $user->email }}</p>
    </div>
@endforeach

{{-- For loop --}}
@for($i = 0; $i < 10; $i++)
    <p>Number: {{ $i }}</p>
@endfor

{{-- While loop --}}
@while($condition)
    <p>Loop continues...</p>
@endwhile

{{-- Forelse (foreach with empty fallback) --}}
@forelse($posts as $post)
    <article>
        <h2>{{ $post->title }}</h2>
        <p>{{ $post->content }}</p>
    </article>
@empty
    <p>No posts found. <a href="{{ route('posts.create') }}">Create one!</a></p>
@endforelse

{{-- The $loop variable (available inside loops) --}}
@foreach($users as $user)
    <div class="user {{ $loop->even ? 'bg-gray' : 'bg-white' }}">
        {{-- $loop->first is true on first iteration --}}
        @if($loop->first)
            <p>👑 First User</p>
        @endif

        <p>{{ $loop->iteration }}. {{ $user->name }}</p>
        
        {{-- $loop->last is true on last iteration --}}
        @if($loop->last)
            <p>Last user in list</p>
        @endif
    </div>
@endforeach
```

**The $loop variable properties**:
- `$loop->index` - Zero-based index (0, 1, 2...)
- `$loop->iteration` - Current iteration (1, 2, 3...)
- `$loop->remaining` - Iterations remaining
- `$loop->count` - Total items in array
- `$loop->first` - True if first iteration
- `$loop->last` - True if last iteration
- `$loop->even` - True if even iteration
- `$loop->odd` - True if odd iteration
- `$loop->depth` - Nesting level of current loop
- `$loop->parent` - Parent loop variable in nested loops

### Layout & Components (Template Inheritance)

One of Blade's most powerful features is template inheritance. Create a master layout, then extend it in child views.

#### Master Layout

```php
{{-- resources/views/layouts/app.blade.php --}}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    
    {{-- Dynamic title --}}
    <title>@yield('title', 'My Awesome Site')</title>
    
    {{-- CSRF token for forms --}}
    <meta name="csrf-token" content="{{ csrf_token() }}">
    
    {{-- CSS --}}
    <link rel="stylesheet" href="{{ asset('css/app.css') }}">
    
    {{-- Additional styles from child views go here --}}
    @stack('styles')
</head>
<body>
    {{-- Navigation --}}
    <nav>
        <a href="{{ route('home') }}">Home</a>
        <a href="{{ route('posts.index') }}">Blog</a>
        
        @auth
            <a href="{{ route('dashboard') }}">Dashboard</a>
            <form action="{{ route('logout') }}" method="POST" style="display: inline;">
                @csrf
                <button>Logout</button>
            </form>
        @else
            <a href="{{ route('login') }}">Login</a>
            <a href="{{ route('register') }}">Register</a>
        @endauth
    </nav>

    {{-- Flash messages --}}
    @if(session('success'))
        <div class="alert alert-success">
            {{ session('success') }}
        </div>
    @endif

    @if(session('error'))
        <div class="alert alert-danger">
            {{ session('error') }}
        </div>
    @endif

    {{-- Main content area (filled by child views) --}}
    <main class="container">
        @yield('content')
    </main>

    {{-- Footer --}}
    <footer>
        @yield('footer', '<p>&copy; 2024 My Site. All rights reserved.</p>')
    </footer>

    {{-- JavaScript --}}
    <script src="{{ asset('js/app.js') }}"></script>
    
    {{-- Additional scripts from child views go here --}}
    @stack('scripts')
</body>
</html>
```

**Explanation**:
- `@yield('name')` - Creates a placeholder that child views fill
- `@yield('name', 'default')` - Placeholder with default content
- `@stack('name')` - Creates a stack where child views can push content

#### Child View

```php
{{-- resources/views/posts/index.blade.php --}}

{{-- Extend the master layout --}}
@extends('layouts.app')

{{-- Set the page title --}}
@section('title', 'All Blog Posts')

{{-- Push additional CSS --}}
@push('styles')
    <link rel="stylesheet" href="{{ asset('css/posts.css') }}">
    <style>
        .post-card {
            margin-bottom: 20px;
            padding: 15px;
            border: 1px solid #ddd;
        }
    </style>
@endpush

{{-- Fill the 'content' section --}}
@section('content')
    <h1>All Blog Posts</h1>
    
    <a href="{{ route('posts.create') }}" class="btn btn-primary">
        Create New Post
    </a>

    <div class="posts-grid">
        @forelse($posts as $post)
            <article class="post-card">
                <h2>
                    <a href="{{ route('posts.show', $post) }}">
                        {{ $post->title }}
                    </a>
                </h2>
                
                <div class="meta">
                    <span>By {{ $post->user->name }}</span>
                    <span>{{ $post->created_at->diffForHumans() }}</span>
                </div>
                
                <p>{{ Str::limit($post->content, 200) }}</p>
                
                <a href="{{ route('posts.show', $post) }}" class="read-more">
                    Read more →
                </a>
            </article>
        @empty
            <p>No posts yet. Be the first to create one!</p>
        @endforelse
    </div>

    {{-- Pagination links --}}
    {{ $posts->links() }}
@endsection

{{-- Push additional JavaScript --}}
@push('scripts')
    <script>
        console.log('Posts page loaded');
        // Additional JavaScript for this page
    </script>
@endpush
```

**How it works**:
1. `@extends('layouts.app')` - Use the master layout
2. `@section('content')` - Fill the content placeholder
3. `@push('styles')` - Add CSS to the styles stack
4. `@push('scripts')` - Add JavaScript to the scripts stack

**Result**: The child view's content is injected into the master layout, creating a complete HTML page.

### Including Sub-Views

```php
{{-- Include another view --}}
@include('partials.header')

{{-- Include with data --}}
@include('partials.user-card', ['user' => $user])

{{-- Include if view exists --}}
@includeIf('partials.sidebar')

{{-- Include when condition is true --}}
@includeWhen($user->isAdmin(), 'partials.admin-panel')

{{-- Include unless condition is true --}}
@includeUnless($user->isBanned(), 'partials.comment-form')

{{-- Include first view that exists --}}
@includeFirst(['partials.custom-header', 'partials.header'])
```

**Example - Reusable User Card**:

```php
{{-- resources/views/partials/user-card.blade.php --}}
<div class="user-card">
    <img src="{{ $user->avatar }}" alt="{{ $user->name }}">
    <h3>{{ $user->name }}</h3>
    <p>{{ $user->email }}</p>
    <p>Member since {{ $user->created_at->format('M Y') }}</p>
</div>

{{-- Use it anywhere --}}
@foreach($users as $user)
    @include('partials.user-card', ['user' => $user])
@endforeach
```

### Blade Components (Modern Approach)

Components are reusable pieces of UI that can accept data and have their own logic.

```bash
# Create a component
php artisan make:component Alert
```

**Component Class**:
```php
{{-- app/View/Components/Alert.php --}}
namespace App\View\Components;

use Illuminate\View\Component;

class Alert extends Component
{
    public $type;
    public $message;
    public $dismissible;

    /**
     * Create a new component instance.
     */
    public function __construct($type = 'info', $message = '', $dismissible = false)
    {
        $this->type = $type;
        $this->message = $message;
        $this->dismissible = $dismissible;
    }

    /**
     * Get the CSS class based on alert type
     */
    public function alertClass()
    {
        return [
            'success' => 'bg-green-100 text-green-800',
            'error' => 'bg-red-100 text-red-800',
            'warning' => 'bg-yellow-100 text-yellow-800',
            'info' => 'bg-blue-100 text-blue-800',
        ][$this->type] ?? 'bg-gray-100 text-gray-800';
    }

    /**
     * Get the icon for alert type
     */
    public function icon()
    {
        return [
            'success' => '✓',
            'error' => '✗',
            'warning' => '⚠',
            'info' => 'ℹ',
        ][$this->type] ?? 'ℹ';
    }

    /**
     * Get the view / contents that represent the component.
     */
    public function render()
    {
        return view('components.alert');
    }
}
```

**Component View**:
```php
{{-- resources/views/components/alert.blade.php --}}
<div class="alert {{ $alertClass() }} {{ $dismissible ? 'dismissible' : '' }}">
    <span class="icon">{{ $icon() }}</span>
    
    <div class="content">
        @if($message)
            {{ $message }}
        @else
            {{ $slot }}
        @endif
    </div>
    
    @if($dismissible)
        <button class="close" onclick="this.parentElement.remove()">×</button>
    @endif
</div>
```

**Using the Component**:
```php
{{-- Method 1: With attributes --}}
<x-alert type="success" message="Profile updated successfully!" />

{{-- Method 2: With slot (content between tags) --}}
<x-alert type="error" dismissible>
    <strong>Error!</strong> Something went wrong.
</x-alert>

{{-- Method 3: Dynamic type --}}
<x-alert :type="$alertType" :message="$alertMessage" />
```

**Anonymous Components** (simpler, no class needed):
```bash
php artisan make:component forms.input --view
```

```php
{{-- resources/views/components/forms/input.blade.php --}}
<div class="form-group">
    <label for="{{ $name }}">{{ $label }}</label>
    <input 
        type="{{ $type ?? 'text' }}" 
        name="{{ $name }}" 
        id="{{ $name }}"
        class="form-control {{ $errors->has($name) ? 'is-invalid' : '' }}"
        value="{{ old($name, $value ?? '') }}"
        {{ $attributes }}
    >
    @error($name)
        <div class="invalid-feedback">{{ $message }}</div>
    @enderror
</div>

{{-- Usage --}}
<x-forms.input 
    name="email" 
    label="Email Address" 
    type="email" 
    required 
/>

<x-forms.input 
    name="bio" 
    label="Biography" 
    placeholder="Tell us about yourself..."
/>
```

**Why use components?**
- **Reusability**: Write once, use everywhere
- **Consistency**: Same look and behavior across your app
- **Maintainability**: Update in one place, changes reflect everywhere
- **Clean Code**: Views become much more readable

---

## 5. Database & Migrations

### What are Migrations?
Migrations are like **version control for your database**. Instead of manually creating tables with SQL, you write PHP code that Laravel converts to SQL. This means:
- Your database structure is tracked in code
- Team members can easily sync database changes
- You can rollback changes if needed
- Works with any database (MySQL, PostgreSQL, SQLite, etc.)

### Database Configuration

```php
// .env file - Configure your database connection
DB_CONNECTION=mysql        // Database type (mysql, pgsql, sqlite, sqlsrv)
DB_HOST=127.0.0.1         // Where database is hosted
DB_PORT=3306              // Database port
DB_DATABASE=laravel       // Database name
DB_USERNAME=root          // Database username
DB_PASSWORD=secret        // Database password
```

**Test your connection**:
```bash
php artisan migrate:status
# If this works, your database is connected!
```

### Creating Migrations

```bash
# Create a migration for a new table
php artisan make:migration create_posts_table

# Create migration with model
php artisan make:model Post -m

# Create migration for modifying existing table
php artisan make:migration add_status_to_posts_table --table=posts

# Create migration for adding columns
php artisan make:migration add_published_at_to_posts_table
```

### Basic Migration Structure

```php
// database/migrations/2024_01_15_000000_create_posts_table.php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations - executed when you run "php artisan migrate"
     */
    public function up(): void
    {
        Schema::create('posts', function (Blueprint $table) {
            // Auto-incrementing ID (primary key)
            $table->id();
            
            // String columns (VARCHAR)
            $table->string('title');                    // VARCHAR(255)
            $table->string('slug')->unique();          // VARCHAR(255) UNIQUE
            $table->string('excerpt', 500)->nullable(); // VARCHAR(500), optional
            
            // Text columns (for long content)
            $table->text('content');                    // TEXT
            $table->longText('body')->nullable();       // LONGTEXT, optional
            
            // Integer columns
            $table->integer('views')->default(0);       // INT with default value
            $table->unsignedBigInteger('user_id');      // BIGINT UNSIGNED (for foreign keys)
            
            // Boolean column
            $table->boolean('is_published')->default(false);
            
            // Enum column (predefined values)
            $table->enum('status', ['draft', 'published', 'archived'])->default('draft');
            
            // Timestamp columns
            $table->timestamp('published_at')->nullable();
            $table->timestamps();  // Creates created_at and updated_at
            
            // Soft deletes (deleted_at column)
            $table->softDeletes();
            
            // Foreign key constraint
            $table->foreign('user_id')
                  ->references('id')
                  ->on('users')
                  ->onDelete('cascade');  // Delete posts when user is deleted
        });
    }

    /**
     * Reverse the migrations - executed when you run "php artisan migrate:rollback"
     */
    public function down(): void
    {
        Schema::dropIfExists('posts');
    }
};
```

### Column Types Reference

```php
// Numeric
$table->id();                              // Auto-incrementing BIGINT UNSIGNED
$table->bigInteger('votes');               // BIGINT
$table->integer('votes');                  // INT
$table->smallInteger('votes');             // SMALLINT
$table->tinyInteger('votes');              // TINYINT
$table->decimal('amount', 8, 2);           // DECIMAL(8,2)
$table->float('amount', 8, 2);             // FLOAT(8,2)
$table->double('amount', 8, 2);            // DOUBLE(8,2)

// String & Text
$table->string('name', 100);               // VARCHAR(100)
$table->text('description');               // TEXT
$table->mediumText('description');         // MEDIUMTEXT
$table->longText('description');           // LONGTEXT
$table->char('code', 4);                   // CHAR(4) - fixed length

// Date & Time
$table->date('birth_date');                // DATE
$table->dateTime('created_at');            // DATETIME
$table->time('sunrise');                   // TIME
$table->timestamp('added_on');             // TIMESTAMP
$table->timestamps();                      // created_at & updated_at
$table->timestampsTz();                    // created_at & updated_at with timezone

// Boolean
$table->boolean('confirmed');              // BOOLEAN (TINYINT(1))

// Binary
$table->binary('data');                    // BLOB

// Other
$table->json('options');                   // JSON
$table->jsonb('options');                  // JSONB (PostgreSQL)
$table->uuid('id');                        // UUID
$table->ipAddress('visitor');              // IP address
$table->macAddress('device');              // MAC address
```

### Column Modifiers

```php
$table->string('email')->nullable();           // Allow NULL values
$table->string('name')->default('Guest');      // Default value
$table->string('slug')->unique();              // Unique constraint
$table->integer('votes')->unsigned();          // UNSIGNED (no negative numbers)
$table->timestamp('created_at')->useCurrent(); // Use current timestamp as default
$table->text('bio')->comment('User biography'); // Add comment to column
$table->string('email')->after('name');        // Place column after another
$table->integer('order')->first();             // Place column first
```

### Foreign Keys Explained

```php
// Method 1: Explicit foreign key
$table->unsignedBigInteger('user_id');
$table->foreign('user_id')
      ->references('id')
      ->on('users')
      ->onDelete('cascade')      // Delete posts when user is deleted
      ->onUpdate('cascade');     // Update posts when user id changes

// Method 2: foreignId helper (Laravel 7+)
$table->foreignId('user_id')
      ->constrained()            // Automatically references 'id' on 'users' table
      ->onDelete('cascade');

// Method 3: Custom table/column
$table->foreignId('author_id')
      ->constrained('users')     // References 'users' table
      ->onDelete('set null');

// Foreign key actions:
// cascade   - Delete/update related records
// set null  - Set foreign key to NULL
// restrict  - Prevent deletion if related records exist
// no action - Do nothing (may cause errors)
```

**Real-world example**: If you delete a user, `onDelete('cascade')` automatically deletes all their posts. Without it, you'd get an error because posts still reference that user.

### Modifying Columns

```php
// database/migrations/2024_01_20_add_status_to_posts_table.php

public function up(): void
{
    Schema::table('posts', function (Blueprint $table) {
        // Add new columns
        $table->string('status')->after('title');
        $table->timestamp('published_at')->nullable()->after('content');
    });
}

public function down(): void
{
    Schema::table('posts', function (Blueprint $table) {
        // Remove columns when rolling back
        $table->dropColumn(['status', 'published_at']);
    });
}
```

**Modifying existing columns** (requires doctrine/dbal package):
```bash
composer require doctrine/dbal
```

```php
public function up(): void
{
    Schema::table('posts', function (Blueprint $table) {
        // Change column type
        $table->string('title', 500)->change();
        
        // Make column nullable
        $table->text('content')->nullable()->change();
        
        // Rename column
        $table->renameColumn('body', 'content');
    });
}
```

### Running Migrations

```bash
# Run all pending migrations
php artisan migrate

# Check migration status
php artisan migrate:status

# Rollback the last batch of migrations
php artisan migrate:rollback

# Rollback specific number of migrations
php artisan migrate:rollback --step=3

# Rollback all migrations
php artisan migrate:reset

# Rollback all and re-run (BE CAREFUL - deletes all data!)
php artisan migrate:refresh

# Refresh and run seeders
php artisan migrate:refresh --seed

# Drop all tables and re-run migrations (DANGEROUS!)
php artisan migrate:fresh

# Run migrations for specific path
php artisan migrate --path=/database/migrations/2024_01_15_create_posts_table.php
```

**Important**: In production, NEVER use `migrate:fresh` or `migrate:refresh` - they delete all data!

### Database Seeders

Seeders fill your database with test data. Useful for development and testing.

```bash
# Create a seeder
php artisan make:seeder UserSeeder
```

```php
// database/seeders/UserSeeder.php

namespace Database\Seeders;

use Illuminate\Database\Seeder;
use App\Models\User;
use Illuminate\Support\Facades\Hash;

class UserSeeder extends Seeder
{
    public function run(): void
    {
        // Create an admin user
        User::create([
            'name' => 'Admin User',
            'email' => 'admin@example.com',
            'password' => Hash::make('password'),
            'is_admin' => true,
        ]);

        // Create 50 random users using factory
        User::factory()->count(50)->create();
        
        // Create specific test users
        User::factory()->count(10)->create([
            'is_verified' => true,
        ]);
    }
}
```

```php
// database/seeders/DatabaseSeeder.php

namespace Database\Seeders;

use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    public function run(): void
    {
        // Run seeders in order
        $this->call([
            UserSeeder::class,
            CategorySeeder::class,
            PostSeeder::class,
            CommentSeeder::class,
        ]);
    }
}
```

**Run seeders**:
```bash
# Run all seeders
php artisan db:seed

# Run specific seeder
php artisan db:seed --class=UserSeeder

# Migrate and seed together
php artisan migrate --seed
php artisan migrate:fresh --seed
```

### Factories (Generate Fake Data)

Factories use the Faker library to generate realistic test data.

```bash
# Create a factory
php artisan make:factory PostFactory
```

```php
// database/factories/PostFactory.php

namespace Database\Factories;

use App\Models\User;
use Illuminate\Database\Eloquent\Factories\Factory;
use Illuminate\Support\Str;

class PostFactory extends Factory
{
    public function definition(): array
    {
        $title = fake()->sentence();
        
        return [
            'user_id' => User::factory(),              // Create associated user
            'title' => $title,
            'slug' => Str::slug($title),
            'excerpt' => fake()->paragraph(),
            'content' => fake()->paragraphs(5, true),  // 5 paragraphs as string
            'views' => fake()->numberBetween(0, 10000),
            'is_published' => fake()->boolean(80),     // 80% true
            'published_at' => fake()->dateTimeBetween('-1 year', 'now'),
        ];
    }
    
    /**
     * Define a published state
     */
    public function published(): static
    {
        return $this->state(fn (array $attributes) => [
            'is_published' => true,
            'published_at' => now(),
        ]);
    }
    
    /**
     * Define a draft state
     */
    public function draft(): static
    {
        return $this->state(fn (array $attributes) => [
            'is_published' => false,
            'published_at' => null,
        ]);
    }
}
```

**Using Factories**:
```php
// Create one post
$post = Post::factory()->create();

// Create 10 posts
$posts = Post::factory()->count(10)->create();

// Create with specific attributes
$post = Post::factory()->create([
    'title' => 'Custom Title',
    'user_id' => 1,
]);

// Use states
$publishedPost = Post::factory()->published()->create();
$draftPost = Post::factory()->draft()->create();

// Create with relationships
$user = User::factory()
    ->has(Post::factory()->count(5))  // User with 5 posts
    ->create();

// Alternative relationship syntax
$user = User::factory()
    ->hasPosts(5)                     // Same as above
    ->create();
```

**Common Faker methods**:
```php
fake()->name();                    // John Doe
fake()->email();                   // john@example.com
fake()->sentence();                // A random sentence
fake()->paragraph();               // A random paragraph
fake()->text(200);                 // Random text, 200 chars
fake()->numberBetween(1, 100);     // 42
fake()->randomFloat(2, 0, 1000);   // 123.45
fake()->boolean();                 // true or false
fake()->date();                    // 2024-01-15
fake()->dateTimeBetween('-1 year', 'now'); // Random date in past year
fake()->imageUrl(640, 480);        // https://via.placeholder.com/640x480
fake()->word();                    // random
fake()->words(3, true);            // random three words
fake()->address();                 // 123 Main St, Springfield
fake()->city();                    // New York
fake()->country();                 // United States
```

---

## 6. Eloquent ORM

### What is Eloquent?
Eloquent is Laravel's ORM (Object-Relational Mapping). Instead of writing SQL queries, you work with PHP objects. Each database table has a corresponding "Model" class.

**Traditional SQL** (tedious):
```php
$users = DB::select('SELECT * FROM users WHERE active = 1');
```

**Eloquent** (elegant):
```php
$users = User::where('active', 1)->get();
```

### Creating Models

```bash
# Basic model
php artisan make:model Post

# Model with migration
php artisan make:model Post -m

# Model with migration, factory, and seeder
php artisan make:model Post -mfs

# Model with controller
php artisan make:model Post -c

# Model with everything (migration, factory, seeder, controller, resource controller)
php artisan make:model Post -a
```

### Basic Model Structure

```php
// app/Models/Post.php

namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;

class Post extends Model
{
    use HasFactory, SoftDeletes;

    /**
     * The table associated with the model.
     * By default, Laravel uses the plural snake_case of the model name
     * Post → posts, UserProfile → user_profiles
     */
    protected $table = 'posts';

    /**
     * The primary key column name
     * Default is 'id'
     */
    protected $primaryKey = 'id';

    /**
     * Indicates if the IDs are auto-incrementing
     * Default is true
     */
    public $incrementing = true;

    /**
     * The data type of the primary key
     * Default is 'int'
     */
    protected $keyType = 'int';

    /**
     * Indicates if the model should be timestamped
     * If true, Laravel automatically manages created_at and updated_at
     * Default is true
     */
    public $timestamps = true;

    /**
     * The attributes that are mass assignable
     * These fields can be set using create() or update()
     */
    protected $fillable = [
        'title',
        'slug',
        'content',
        'excerpt',
        'user_id',
        'published_at',
    ];

    /**
     * The attributes that aren't mass assignable
     * Use either $fillable OR $guarded, not both
     */
    protected $guarded = [
        'id',
        'views',  // Prevent direct mass assignment of views
    ];

    /**
     * The attributes that should be hidden for arrays/JSON
     * Useful for passwords, tokens, etc.
     */
    protected $hidden = [
        'deleted_at',
    ];

    /**
     * The attributes that should be cast to native types
     */
    protected $casts = [
        'published_at' => 'datetime',      // Cast to Carbon instance
        'is_published' => 'boolean',       // Cast to boolean
        'views' => 'integer',              // Cast to integer
        'meta' => 'array',                 // JSON to array
        'settings' => 'object',            // JSON to object
    ];

    /**
     * The attributes that should be mutated to dates
     * (Alternative to using casts for dates)
     */
    protected $dates = [
        'published_at',
        'scheduled_at',
    ];

    /**
     * Default attribute values
     */
    protected $attributes = [
        'views' => 0,
        'is_published' => false,
    ];
}
```

### CRUD Operations

#### Create (Insert)

```php
// Method 1: Using create() - requires $fillable
$post = Post::create([
    'title' => 'My First Post',
    'slug' => 'my-first-post',
    'content' => 'This is the content...',
    'user_id' => 1,
]);

// Method 2: Using new and save()
$post = new Post();
$post->title = 'My First Post';
$post->slug = 'my-first-post';
$post->content = 'This is the content...';
$post->user_id = 1;
$post->save();

// Method 3: firstOrCreate (find or create)
$post = Post::firstOrCreate(
    ['slug' => 'my-first-post'],     // Search criteria
    [                                 // Additional data if creating
        'title' => 'My First Post',
        'content' => 'Content here...',
        'user_id' => 1,
    ]
);

// Method 4: updateOrCreate (find and update, or create)
$post = Post::updateOrCreate(
    ['slug' => 'my-first-post'],     // Search criteria
    [                                 // Data to update/create
        'title' => 'Updated Title',
        'content' => 'Updated content...',
    ]
);
```

**Mass Assignment Protection**:
```php
// This works - title is in $fillable
$post = Post::create(['title' => 'Test']);

// This throws error - id is not in $fillable (or is in $guarded)
$post = Post::create(['id' => 999, 'title' => 'Test']);

// To bypass (use carefully!)
$post = Post::create([
    'title' => 'Test',
], true);  // Second parameter bypasses protection
```

#### Read (Select)

```php
// Get all records
$posts = Post::all();

// Get first record
$post = Post::first();

// Find by primary key
$post = Post::find(1);
$post = Post::find([1, 2, 3]);  // Multiple IDs

// Find or throw 404 exception
$post = Post::findOrFail(1);

// Where clauses
$posts = Post::where('is_published', true)->get();
$posts = Post::where('views', '>', 100)->get();
$posts = Post::where('title', 'like', '%Laravel%')->get();

// Multiple where conditions
$posts = Post::where('is_published', true)
             ->where('views', '>', 100)
             ->get();

// OR conditions
$posts = Post::where('views', '>', 1000)
             ->orWhere('is_featured', true)
             ->get();

// Where between
$posts = Post::whereBetween('views', [100, 1000])->get();

// Where in
$posts = Post::whereIn('status', ['published', 'featured'])->get();

// Where null
$posts = Post::whereNull('deleted_at')->get();
$posts = Post::whereNotNull('published_at')->get();

// Where date
$posts = Post::whereDate('created_at', '2024-01-15')->get();
$posts = Post::whereYear('created_at', 2024)->get();
$posts = Post::whereMonth('created_at', 1)->get();

// Latest and oldest
$posts = Post::latest()->get();           // ORDER BY created_at DESC
$posts = Post::oldest()->get();           // ORDER BY created_at ASC
$posts = Post::latest('published_at')->get();  // Custom column

// Ordering
$posts = Post::orderBy('views', 'desc')->get();
$posts = Post::orderBy('title')->get();   // Default is ASC

// Limit and offset
$posts = Post::take(10)->get();           // LIMIT 10
$posts = Post::skip(20)->take(10)->get(); // OFFSET 20 LIMIT 10

// Get single column value
$title = Post::where('id', 1)->value('title');

// Pluck (get array of single column)
$titles = Post::pluck('title');           // ['Title 1', 'Title 2', ...]
$titles = Post::pluck('title', 'id');     // [1 => 'Title 1', 2 => 'Title 2', ...]

// Count, sum, avg, max, min
$count = Post::count();
$sum = Post::sum('views');
$avg = Post::avg('views');
$max = Post::max('views');
$min = Post::min('views');

// Exists and doesn't exist
if (Post::where('slug', 'test')->exists()) {
    // Post exists
}

if (Post::where('slug', 'test')->doesntExist()) {
    // Post doesn't exist
}
```

#### Update

```php
// Method 1: Find and update
$post = Post::find(1);
$post->title = 'Updated Title';
$post->content = 'Updated content...';
$post->save();

// Method 2: Mass update
Post::where('is_published', false)
    ->update(['status' => 'draft']);

// Method 3: Update specific record
$post = Post::find(1);
$post->update([
    'title' => 'New Title',
    'content' => 'New content...',
]);

// Increment/Decrement
$post->increment('views');              // views + 1
$post->increment('views', 5);           // views + 5
$post->decrement('votes');              // votes - 1
$post->decrement('votes', 3);           // votes - 3

// Increment with additional updates
$post->increment('views', 1, [
    'last_viewed_at' => now(),
]);
```

#### Delete

```php
// Method 1: Find and delete
$post = Post::find(1);
$post->delete();

// Method 2: Delete by ID
Post::destroy(1);
Post::destroy([1, 2, 3]);  // Multiple IDs

// Method 3: Delete with query
Post::where('views', '<', 10)->delete();

// Soft Delete (requires SoftDeletes trait)
$post->delete();  // Sets deleted_at timestamp, doesn't actually delete

// Get soft deleted records
$posts = Post::withTrashed()->get();     // All records including deleted
$posts = Post::onlyTrashed()->get();     // Only deleted records

// Restore soft deleted record
$post = Post::withTrashed()->find(1);
$post->restore();

// Permanently delete (force delete)
$post->forceDelete();
```

### Query Scopes

Scopes let you reuse query logic. Think of them as shortcuts for common queries.

```php
// app/Models/Post.php

class Post extends Model
{
    /**
     * Scope for published posts
     * Usage: Post::published()->get()
     */
    public function scopePublished($query)
    {
        return $query->where('is_published', true)
                    ->whereNotNull('published_at');
    }

    /**
     * Scope for popular posts
     * Usage: Post::popular()->get()
     */
    public function scopePopular($query, $minViews = 1000)
    {
        return $query->where('views', '>=', $minViews);
    }

    /**
     * Scope for posts by specific user
     * Usage: Post::byUser($userId)->get()
     */
    public function scopeByUser($query, $userId)
    {
        return $query->where('user_id', $userId);
    }

    /**
     * Scope for recent posts
     * Usage: Post::recent()->get()
     */
    public function scopeRecent($query, $days = 7)
    {
        return $query->where('created_at', '>=', now()->subDays($days));
    }
}
```

**Using Scopes**:
```php
// Single scope
$posts = Post::published()->get();

// Chain multiple scopes
$posts = Post::published()
            ->popular()
            ->recent(30)
            ->get();

// Combine with regular queries
$posts = Post::published()
            ->where('user_id', 1)
            ->orderBy('views', 'desc')
            ->paginate(15);

// Scope with parameters
$posts = Post::popular(5000)->get();  // Posts with 5000+ views
```

### Accessors & Mutators (Get & Set Attributes)

Accessors and mutators let you format attribute values when getting or setting them.

```php
// app/Models/User.php

class User extends Model
{
    /**
     * Accessor: Get attribute (Laravel 9+)
     * Automatically called when accessing $user->name
     */
    protected function name(): Attribute
    {
        return Attribute::make(
            get: fn ($value) => ucwords($value),  // Capitalize each word
        );
    }

    /**
     * Mutator: Set attribute (Laravel 9+)
     * Automatically called when setting $user->name = 'john doe';
     */
    protected function name(): Attribute
    {
        return Attribute::make(
            get: fn ($value) => ucwords($value),
            set: fn ($value) => strtolower($value),  // Store as lowercase
        );
    }

    /**
     * Old style accessor (Laravel 8 and below)
     */
    public function getNameAttribute($value)
    {
        return ucwords($value);
    }

    /**
     * Old style mutator (Laravel 8 and below)
     */
    public function setNameAttribute($value)
    {
        $this->attributes['name'] = strtolower($value);
    }

    /**
     * Accessor for full name (virtual attribute)
     */
    protected function fullName(): Attribute
    {
        return Attribute::make(
            get: fn () => "{$this->first_name} {$this->last_name}",
        );
    }
    
    // Usage: $user->full_name (no column needed in database!)
}
```

**More Examples**:
```php
class Post extends Model
{
    /**
     * Format title for display
     */
    protected function title(): Attribute
    {
        return Attribute::make(
            get: fn ($value) => ucfirst($value),
        );
    }

    /**
     * Always hash password when setting
     */
    protected function password(): Attribute
    {
        return Attribute::make(
            set: fn ($value) => bcrypt($value),
        );
    }

    /**
     * Get excerpt or generate from content
     */
    protected function excerpt(): Attribute
    {
        return Attribute::make(
            get: fn ($value) => $value ?? Str::limit($this->content, 200),
        );
    }
}
```

**When to use**:
- **Accessors**: Format data for display (capitalize names, format dates)
- **Mutators**: Clean/format data before saving (lowercase emails, hash passwords)
- **Virtual Attributes**: Create attributes that don't exist in database (full_name from first_name + last_name)

### Pagination

```php
// Simple pagination (15 items per page)
$posts = Post::paginate(15);

// Custom per page
$posts = Post::paginate(50);

// Simple pagination (only next/previous, faster)
$posts = Post::simplePaginate(15);

// Get paginated published posts
$posts = Post::published()->latest()->paginate(20);

// In Blade view
{{ $posts->links() }}  {{-- Pagination buttons --}}

// Customize pagination view
{{ $posts->links('pagination::bootstrap-4') }}
{{ $posts->links('pagination::tailwind') }}

// Get pagination info
{{ $posts->total() }}        // Total items
{{ $posts->currentPage() }}  // Current page number
{{ $posts