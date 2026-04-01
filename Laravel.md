# 🔴 Laravel Complete Notes — Everything You Need

> A complete reference guide covering every concept, pattern, and feature of Laravel with code examples and explanations. Built for real-world use.

---

## Table of Contents

1. [What is Laravel?](#1-what-is-laravel)
2. [Installation & Project Setup](#2-installation--project-setup)
3. [Project Structure](#3-project-structure)
4. [Configuration & Environment](#4-configuration--environment)
5. [Routing](#5-routing)
6. [Controllers](#6-controllers)
7. [Request & Response](#7-request--response)
8. [Middleware](#8-middleware)
9. [Views & Blade Templates](#9-views--blade-templates)
10. [Models & Eloquent ORM](#10-models--eloquent-orm)
11. [Migrations](#11-migrations)
12. [Seeders & Factories](#12-seeders--factories)
13. [Eloquent Relationships](#13-eloquent-relationships)
14. [Query Builder](#14-query-builder)
15. [Collections](#15-collections)
16. [Validation](#16-validation)
17. [Authentication](#17-authentication)
18. [Authorization (Gates & Policies)](#18-authorization-gates--policies)
19. [File Storage](#19-file-storage)
20. [Email & Notifications](#20-email--notifications)
21. [Queues & Jobs](#21-queues--jobs)
22. [Task Scheduling](#22-task-scheduling)
23. [Events & Listeners](#23-events--listeners)
24. [Service Container & Providers](#24-service-container--providers)
25. [Facades](#25-facades)
26. [Caching](#26-caching)
27. [Sessions](#27-sessions)
28. [Logging](#28-logging)
29. [Testing](#29-testing)
30. [API Development (with Sanctum)](#30-api-development-with-sanctum)
31. [Broadcasting (WebSockets)](#31-broadcasting-websockets)
32. [Artisan Console Commands](#32-artisan-console-commands)
33. [Deployment](#33-deployment)
34. [Security Best Practices](#34-security-best-practices)
35. [Common Patterns & Tips](#35-common-patterns--tips)
36. [Localization](#36-localization)

---

## 1. What is Laravel?

Laravel is a **free, open-source PHP web framework** created by Taylor Otwell. It follows the **MVC (Model-View-Controller)** pattern and is known for its elegant syntax, powerful features, and massive ecosystem.

### Why Laravel?

- **Expressive, elegant syntax** — clean code that reads naturally
- **Batteries included** — Auth, ORM, queues, mail, cache, broadcasting all built-in
- **Eloquent ORM** — one of the best ORMs in any language
- **Blade templating** — powerful, fast template engine
- **Artisan CLI** — automate everything from the terminal
- **Ecosystem** — Laravel Forge, Vapor, Horizon, Telescope, Octane, Jetstream, Breeze, Livewire, Inertia

### Laravel vs Other Frameworks

| Feature | Laravel | Symfony | CodeIgniter | Lumen |
|---------|---------|---------|-------------|-------|
| Type | Full-stack | Full-stack | Lightweight | Micro |
| ORM | Eloquent | Doctrine | Active Record | Eloquent |
| Template | Blade | Twig | PHP | None |
| Ecosystem | Huge | Large | Small | Minimal |
| Best for | Full apps, APIs | Enterprise | Small apps | Microservices |

### Request Lifecycle

```
HTTP Request
    ↓
public/index.php          ← Entry point
    ↓
bootstrap/app.php         ← App instantiation
    ↓
HTTP Kernel               ← Global middleware stack
    ↓
Route Middleware           ← Route-specific middleware
    ↓
Controller / Closure       ← Your logic
    ↓
Response                  ← Sent back to browser
```

---

## 2. Installation & Project Setup

### Requirements

- PHP >= 8.2
- Composer
- Node.js & NPM (for frontend assets)

### Install via Composer

```bash
# Create new project
composer create-project laravel/laravel my-project

# Or via Laravel installer
composer global require laravel/installer
laravel new my-project

# With starter kit options
laravel new my-project --breeze     # Minimal auth scaffolding
laravel new my-project --jet        # Jetstream (teams, 2FA)
laravel new my-project --livewire   # Livewire + Volt
```

### Start Development Server

```bash
cd my-project

# Built-in server
php artisan serve                    # localhost:8000
php artisan serve --port=8080

# With Vite (for frontend)
npm install
npm run dev

# Using Laravel Sail (Docker)
./vendor/bin/sail up
./vendor/bin/sail up -d              # Detached mode
```

### Essential Artisan Commands

```bash
# General
php artisan list                     # List all commands
php artisan help <command>           # Help for a command
php artisan about                    # App info
php artisan env                      # Show environment

# Clear caches
php artisan cache:clear              # Clear application cache
php artisan config:clear             # Clear config cache
php artisan route:clear              # Clear route cache
php artisan view:clear               # Clear compiled views
php artisan optimize:clear           # Clear all caches

# Optimize for production
php artisan config:cache             # Cache config
php artisan route:cache              # Cache routes
php artisan view:cache               # Cache Blade views
php artisan optimize                 # Run all caches

# Generate files
php artisan make:model Post -mcrsf   # Model + Migration + Controller + Resource + Seeder + Factory
php artisan make:controller PostController --resource
php artisan make:middleware CheckAge
php artisan make:request StorePostRequest
php artisan make:resource PostResource
php artisan make:event PostPublished
php artisan make:listener SendPostNotification --event=PostPublished
php artisan make:job ProcessImage
php artisan make:mail WelcomeMail --markdown
php artisan make:notification InvoicePaid
php artisan make:policy PostPolicy --model=Post
php artisan make:seeder PostSeeder
php artisan make:factory PostFactory
php artisan make:command SendEmails
php artisan make:provider AppServiceProvider
php artisan make:observer PostObserver --model=Post
php artisan make:rule Uppercase
php artisan make:channel OrderChannel

# Database
php artisan migrate                  # Run migrations
php artisan migrate:fresh            # Drop all & re-migrate
php artisan migrate:fresh --seed     # Fresh + seed
php artisan migrate:rollback         # Rollback last batch
php artisan migrate:reset            # Rollback all
php artisan migrate:status           # Show migration status
php artisan db:seed                  # Run seeders
php artisan db:seed --class=PostSeeder

# Interactive shell
php artisan tinker                   # REPL with full Laravel context
```

---

## 3. Project Structure

```
my-project/
├── app/
│   ├── Console/
│   │   └── Commands/           ← Custom Artisan commands
│   ├── Events/                 ← Event classes
│   ├── Exceptions/
│   │   └── Handler.php         ← Exception handling
│   ├── Http/
│   │   ├── Controllers/        ← Controllers
│   │   ├── Middleware/         ← Middleware classes
│   │   └── Requests/           ← Form request validation
│   ├── Jobs/                   ← Queued jobs
│   ├── Listeners/              ← Event listeners
│   ├── Mail/                   ← Mailable classes
│   ├── Models/                 ← Eloquent models
│   ├── Notifications/          ← Notification classes
│   ├── Observers/              ← Model observers
│   ├── Policies/               ← Authorization policies
│   ├── Providers/              ← Service providers
│   └── Rules/                  ← Custom validation rules
├── bootstrap/
│   ├── app.php                 ← Application bootstrapping
│   └── cache/                  ← Framework cache
├── config/                     ← All config files
│   ├── app.php
│   ├── auth.php
│   ├── cache.php
│   ├── database.php
│   ├── filesystems.php
│   ├── mail.php
│   ├── queue.php
│   └── ...
├── database/
│   ├── factories/              ← Model factories
│   ├── migrations/             ← Database migrations
│   └── seeders/                ← Database seeders
├── lang/                       ← Localization files
├── public/                     ← Web root (index.php, assets)
├── resources/
│   ├── css/                    ← CSS files
│   ├── js/                     ← JS files
│   └── views/                  ← Blade templates
├── routes/
│   ├── api.php                 ← API routes
│   ├── channels.php            ← Broadcasting channels
│   ├── console.php             ← Artisan closure commands
│   └── web.php                 ← Web routes
├── storage/
│   ├── app/                    ← App-generated files
│   ├── framework/              ← Framework cache/sessions/views
│   └── logs/                   ← Log files
├── tests/
│   ├── Feature/                ← Feature tests
│   └── Unit/                   ← Unit tests
├── vendor/                     ← Composer packages
├── .env                        ← Environment variables
├── .env.example
├── artisan                     ← Artisan CLI entry point
├── composer.json
├── package.json
└── vite.config.js
```

---

## 4. Configuration & Environment

### `.env` File

```env
APP_NAME="My Laravel App"
APP_ENV=local
APP_KEY=base64:...
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=my_database
DB_USERNAME=root
DB_PASSWORD=secret

BROADCAST_DRIVER=log
CACHE_DRIVER=redis
FILESYSTEM_DISK=local
QUEUE_CONNECTION=redis
SESSION_DRIVER=database
SESSION_LIFETIME=120

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=smtp.mailtrap.io
MAIL_PORT=2525
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="noreply@example.com"
MAIL_FROM_NAME="${APP_NAME}"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_HOST=
PUSHER_PORT=443
PUSHER_SCHEME=https
PUSHER_APP_CLUSTER=mt1

VITE_APP_NAME="${APP_NAME}"
VITE_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
```

### Accessing Config Values

```php
// Read env directly (only in config files!)
$value = env('APP_NAME', 'default');

// Read config (use this everywhere else)
$value = config('app.name');
$value = config('database.connections.mysql.host');
$value = config('app.timezone', 'UTC');  // With default

// Set config at runtime
config(['app.name' => 'New Name']);

// Check environment
App::environment('local');             // true if local
App::environment(['local', 'staging']); // true if either
app()->environment('production');
```

### Config Files

```php
// config/app.php — Example structure
return [
    'name'     => env('APP_NAME', 'Laravel'),
    'env'      => env('APP_ENV', 'production'),
    'debug'    => (bool) env('APP_DEBUG', false),
    'url'      => env('APP_URL', 'http://localhost'),
    'timezone' => 'UTC',
    'locale'   => 'en',
    'key'      => env('APP_KEY'),
    'cipher'   => 'AES-256-CBC',
    'providers' => [
        // Service providers...
    ],
    'aliases' => [
        // Facades...
    ],
];

// Custom config file: config/myapp.php
return [
    'feature_x_enabled' => env('FEATURE_X', false),
    'max_upload_size'   => 10240,
    'allowed_types'     => ['jpg', 'png', 'pdf'],
];

// Usage: config('myapp.feature_x_enabled')
```

---

## 5. Routing

### Basic Routes

```php
// routes/web.php
use Illuminate\Support\Facades\Route;
use App\Http\Controllers\PostController;
use App\Http\Controllers\UserController;

// Basic HTTP verbs
Route::get('/posts', [PostController::class, 'index']);
Route::post('/posts', [PostController::class, 'store']);
Route::put('/posts/{id}', [PostController::class, 'update']);
Route::patch('/posts/{id}', [PostController::class, 'update']);
Route::delete('/posts/{id}', [PostController::class, 'destroy']);

// Closure route
Route::get('/hello', function () {
    return 'Hello World!';
});

// Multiple methods
Route::match(['get', 'post'], '/contact', [ContactController::class, 'handle']);
Route::any('/any', [ContactController::class, 'handle']);

// Return view directly
Route::view('/about', 'pages.about');
Route::view('/about', 'pages.about', ['name' => 'Taylor']);

// Redirect
Route::redirect('/here', '/there');
Route::redirect('/here', '/there', 301);   // Permanent
Route::permanentRedirect('/here', '/there');
```

### Route Parameters

```php
// Required parameter
Route::get('/users/{id}', function (string $id) {
    return "User: $id";
});

// Optional parameter
Route::get('/users/{name?}', function (?string $name = 'Guest') {
    return "Hello, $name!";
});

// Multiple parameters
Route::get('/posts/{post}/comments/{comment}', function (string $postId, string $commentId) {
    return "Post: $postId, Comment: $commentId";
});

// Constrain with regex
Route::get('/users/{id}', [UserController::class, 'show'])
    ->where('id', '[0-9]+');

Route::get('/users/{name}', [UserController::class, 'show'])
    ->where('name', '[A-Za-z]+');

Route::get('/posts/{id}/{slug}', [PostController::class, 'show'])
    ->where(['id' => '[0-9]+', 'slug' => '[a-z\-]+']);

// Global constraints — in RouteServiceProvider
Route::pattern('id', '[0-9]+');

// Built-in shorthand constraints
Route::get('/users/{id}', ...)->whereNumber('id');
Route::get('/users/{name}', ...)->whereAlpha('name');
Route::get('/users/{slug}', ...)->whereAlphaNumeric('slug');
Route::get('/posts/{uuid}', ...)->whereUuid('uuid');
Route::get('/posts/{ulid}', ...)->whereUlid('ulid');
```

### Named Routes

```php
Route::get('/profile', [ProfileController::class, 'show'])->name('profile.show');
Route::get('/posts/{id}', [PostController::class, 'show'])->name('posts.show');

// Generate URL
$url = route('profile.show');
$url = route('posts.show', ['id' => 1]);
$url = route('posts.show', ['id' => 1, 'page' => 2]); // Extra params become query string

// Redirect to named route
return redirect()->route('profile.show');
return to_route('posts.show', ['id' => 1]);  // Shorthand

// Check current route
if (request()->routeIs('posts.*')) { ... }
if (request()->routeIs('posts.show', 'posts.index')) { ... }
```

### Route Groups

```php
// Prefix
Route::prefix('admin')->group(function () {
    Route::get('/users', [AdminUserController::class, 'index']);     // /admin/users
    Route::get('/posts', [AdminPostController::class, 'index']);     // /admin/posts
});

// Middleware
Route::middleware(['auth', 'verified'])->group(function () {
    Route::get('/dashboard', [DashboardController::class, 'index']);
    Route::get('/profile', [ProfileController::class, 'show']);
});

// Name prefix
Route::name('admin.')->group(function () {
    Route::get('/admin/users', [AdminUserController::class, 'index'])->name('users.index');
    // route name: admin.users.index
});

// Multiple options combined
Route::prefix('api/v1')
    ->middleware('auth:sanctum')
    ->name('api.')
    ->group(function () {
        Route::apiResource('posts', PostController::class);
        Route::apiResource('users', UserController::class);
    });

// Nested groups
Route::prefix('admin')->middleware('auth')->name('admin.')->group(function () {
    Route::prefix('users')->name('users.')->group(function () {
        Route::get('/', [AdminUserController::class, 'index'])->name('index');
        Route::get('/{id}', [AdminUserController::class, 'show'])->name('show');
    });
});
```

### Resource Routes

```php
// Full CRUD (7 routes)
Route::resource('posts', PostController::class);
// GET    /posts            → index
// GET    /posts/create     → create
// POST   /posts            → store
// GET    /posts/{id}       → show
// GET    /posts/{id}/edit  → edit
// PUT    /posts/{id}       → update
// DELETE /posts/{id}       → destroy

// Partial resource
Route::resource('posts', PostController::class)->only(['index', 'show']);
Route::resource('posts', PostController::class)->except(['create', 'edit', 'destroy']);

// API resource (no create/edit — no HTML forms)
Route::apiResource('posts', PostController::class);

// Multiple resources
Route::resources([
    'posts'    => PostController::class,
    'comments' => CommentController::class,
]);

Route::apiResources([
    'posts'    => PostController::class,
    'comments' => CommentController::class,
]);

// Nested resource
Route::resource('posts.comments', PostCommentController::class);
// /posts/{post}/comments
// /posts/{post}/comments/{comment}

// Shallow nesting (only nested where needed)
Route::resource('posts.comments', PostCommentController::class)->shallow();

// Custom parameter name
Route::resource('users', UserController::class)->parameters(['users' => 'user_id']);
```

### Route Model Binding

```php
// Implicit binding (by primary key — type hint the model)
Route::get('/posts/{post}', function (App\Models\Post $post) {
    return $post;  // Laravel auto-fetches Post where id = {post}
});

// Implicit binding in controller
public function show(Post $post) {
    return response()->json($post);
}

// Bind by different column (slug, uuid, etc.)
// In the model:
public function getRouteKeyName(): string {
    return 'slug';
}

// Or per-route:
Route::get('/posts/{post:slug}', function (Post $post) {
    return $post;
});

// Explicit binding in RouteServiceProvider
Route::bind('post', function (string $value) {
    return Post::where('slug', $value)->firstOrFail();
});

// Explicit binding in a model
public function resolveRouteBinding($value, $field = null) {
    return $this->where($field ?? 'slug', $value)->firstOrFail();
}

// Scoped bindings (ensure comment belongs to post)
Route::get('/posts/{post}/comments/{comment:uuid}', function (Post $post, Comment $comment) {
    // Comment is automatically scoped to the post
});
```

### API Versioning

```php
// routes/api.php
Route::prefix('v1')->name('api.v1.')->group(function () {
    Route::apiResource('posts', V1\PostController::class);
});

Route::prefix('v2')->name('api.v2.')->group(function () {
    Route::apiResource('posts', V2\PostController::class);
});
```

---

## 6. Controllers

### Basic Controller

```php
// app/Http/Controllers/PostController.php
namespace App\Http\Controllers;

use App\Models\Post;
use App\Http\Requests\StorePostRequest;
use App\Http\Requests\UpdatePostRequest;
use App\Http\Resources\PostResource;
use App\Http\Resources\PostCollection;
use Illuminate\Http\Request;
use Illuminate\Http\JsonResponse;
use Illuminate\Http\Response;

class PostController extends Controller
{
    // Constructor — inject dependencies
    public function __construct(
        private readonly PostService $postService,
    ) {}

    // GET /posts
    public function index(Request $request): PostCollection
    {
        $posts = Post::published()
            ->with(['author', 'tags'])
            ->paginate($request->per_page ?? 15);

        return new PostCollection($posts);
    }

    // GET /posts/{id}
    public function show(Post $post): PostResource
    {
        $post->load(['author', 'comments.author', 'tags']);
        return new PostResource($post);
    }

    // POST /posts
    public function store(StorePostRequest $request): JsonResponse
    {
        $post = $this->postService->create(
            $request->validated(),
            $request->user()
        );

        return response()->json(new PostResource($post), 201);
    }

    // PUT/PATCH /posts/{id}
    public function update(UpdatePostRequest $request, Post $post): PostResource
    {
        $post->update($request->validated());
        return new PostResource($post->fresh());
    }

    // DELETE /posts/{id}
    public function destroy(Post $post): Response
    {
        $this->authorize('delete', $post);
        $post->delete();
        return response()->noContent();
    }
}
```

### Resource Controller (stub)

```bash
php artisan make:controller PostController --resource --model=Post
```

```php
class PostController extends Controller
{
    public function index() {}           // GET    /posts
    public function create() {}          // GET    /posts/create
    public function store(Request $r) {} // POST   /posts
    public function show(Post $post) {}  // GET    /posts/{post}
    public function edit(Post $post) {}  // GET    /posts/{post}/edit
    public function update(Request $r, Post $post) {} // PUT/PATCH /posts/{post}
    public function destroy(Post $post) {}            // DELETE    /posts/{post}
}
```

### Single Action Controller

```php
// app/Http/Controllers/PublishPostController.php
class PublishPostController extends Controller
{
    public function __invoke(Post $post): JsonResponse
    {
        $this->authorize('update', $post);
        $post->update(['status' => 'published', 'published_at' => now()]);
        return response()->json(['message' => 'Post published.']);
    }
}

// Route
Route::post('/posts/{post}/publish', PublishPostController::class)->name('posts.publish');
```

### Controller Middleware

```php
class PostController extends Controller
{
    public function __construct()
    {
        // Apply to all methods
        $this->middleware('auth');

        // Apply to specific methods
        $this->middleware('auth')->only(['store', 'update', 'destroy']);

        // Exclude from methods
        $this->middleware('auth')->except(['index', 'show']);
    }
}

// Or per-route (preferred in Laravel 10+)
Route::get('/posts', [PostController::class, 'index'])->middleware('auth');
```

---

## 7. Request & Response

### The Request Object

```php
use Illuminate\Http\Request;

public function store(Request $request)
{
    // --- Input ---
    $name    = $request->input('name');                        // Any input
    $name    = $request->input('name', 'default');             // With default
    $name    = $request->name;                                 // Magic property
    $all     = $request->all();                                // All inputs
    $only    = $request->only(['name', 'email']);              // Specific keys
    $except  = $request->except(['password']);                 // Exclude keys
    $merged  = $request->merge(['active' => true]);            // Merge extra data
    $safe    = $request->safe()->only(['name', 'email']);      // After validation

    // Query string: /posts?page=2&search=laravel
    $page    = $request->query('page', 1);
    $all     = $request->query();

    // Check existence
    $has     = $request->has('name');                          // Key exists & not null
    $filled  = $request->filled('name');                       // Key exists & not empty
    $missing = $request->missing('name');
    $whenHas = $request->whenHas('name', fn($v) => strtoupper($v));
    $anyOf   = $request->hasAny(['name', 'email']);

    // Booleans
    $bool    = $request->boolean('active');                    // '1','true','on','yes' → true

    // Numbers
    $int     = $request->integer('page');
    $float   = $request->float('price');

    // Dates
    $date    = $request->date('birthday');                     // Carbon instance
    $date    = $request->date('birthday', 'Y-m-d', 'UTC');

    // JSON
    $data    = $request->json()->all();                        // If Content-Type: application/json
    $field   = $request->json('user.name');                    // Dot notation

    // --- Files ---
    $file    = $request->file('avatar');
    $hasFile = $request->hasFile('avatar');
    if ($request->hasFile('avatar') && $request->file('avatar')->isValid()) {
        $path = $request->file('avatar')->store('avatars');
        $path = $request->file('avatar')->storeAs('avatars', 'unique.jpg');
        $path = $request->file('avatar')->storePublicly('avatars');
        $name = $request->file('avatar')->getClientOriginalName();
        $ext  = $request->file('avatar')->getClientOriginalExtension();
        $size = $request->file('avatar')->getSize();
        $mime = $request->file('avatar')->getMimeType();
    }
    $files   = $request->file('photos');                       // Multiple files

    // --- Headers ---
    $token   = $request->header('Authorization');
    $token   = $request->bearerToken();                        // Strip "Bearer "
    $type    = $request->header('Content-Type', 'text/html');  // With default

    // --- Request Info ---
    $method  = $request->method();                             // GET, POST, etc.
    $isGet   = $request->isMethod('get');
    $url     = $request->url();                                // Without query string
    $fullUrl = $request->fullUrl();                            // With query string
    $path    = $request->path();                               // /posts/1
    $ip      = $request->ip();
    $ips     = $request->ips();
    $isAjax  = $request->ajax();
    $isPjax  = $request->pjax();
    $wantsJson = $request->wantsJson();
    $expectsJson = $request->expectsJson();
    $isJson  = $request->isJson();
    $secure  = $request->secure();

    // --- Auth ---
    $user    = $request->user();
    $userId  = $request->user()->id;

    // --- Cookies ---
    $cookie  = $request->cookie('name');
    $cookies = $request->cookies->all();

    // --- Session ---
    $value   = $request->session()->get('key');
}
```

### Response Methods

```php
// String / View
return 'Hello World';
return view('welcome');
return view('posts.show', ['post' => $post]);
return view('posts.show')->with('post', $post);

// JSON
return response()->json(['name' => 'John', 'age' => 30]);
return response()->json(['error' => 'Not Found'], 404);
return response()->json($data, 200, ['X-Custom' => 'value'], JSON_PRETTY_PRINT);

// Status codes
return response('Created', 201);
return response()->noContent();         // 204

// Redirect
return redirect('/home');
return redirect()->route('posts.index');
return redirect()->to('https://example.com');
return redirect()->back();
return redirect()->back()->withInput();
return redirect()->route('posts.index')->with('success', 'Post created!');
return redirect()->intended('/dashboard');  // Redirect to original destination

// File download
return response()->download(storage_path('app/files/report.pdf'));
return response()->download($path, 'Report.pdf', $headers);
return response()->file($path);         // Stream inline (e.g., images)

// Streaming response
return response()->stream(function () {
    echo "Hello";
    flush();
    sleep(1);
    echo " World";
}, 200, ['Content-Type' => 'text/plain']);

// Custom headers & cookies
return response()->json($data)
    ->header('X-Header-One', 'value1')
    ->header('X-Header-Two', 'value2')
    ->withHeaders(['X-Custom' => 'value'])
    ->cookie('name', 'value', 60)              // name, value, minutes
    ->cookie('name', 'value', 60, '/', null, true, true); // Secure, HttpOnly

// Macro response (define once, use anywhere)
// In AppServiceProvider boot():
Response::macro('success', function ($data, $message = 'Success', $code = 200) {
    return Response::json([
        'success' => true,
        'message' => $message,
        'data'    => $data,
    ], $code);
});
// Usage: return response()->success($data, 'Post created', 201);
```

### Form Requests (Validation)

```php
// app/Http/Requests/StorePostRequest.php
namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Validation\Rule;

class StorePostRequest extends FormRequest
{
    // Who can make this request?
    public function authorize(): bool
    {
        return $this->user()->can('create', Post::class);
        // return true; // Allow everyone
    }

    // Validation rules
    public function rules(): array
    {
        return [
            'title'       => ['required', 'string', 'min:5', 'max:200'],
            'content'     => ['required', 'string', 'min:50'],
            'category_id' => ['required', 'integer', 'exists:categories,id'],
            'status'      => ['required', Rule::in(['draft', 'published'])],
            'tags'        => ['array', 'max:10'],
            'tags.*'      => ['string', 'max:50'],
            'cover'       => ['nullable', 'image', 'mimes:jpg,jpeg,png', 'max:2048'],
            'published_at'=> ['nullable', 'date', 'after:now'],
        ];
    }

    // Custom messages
    public function messages(): array
    {
        return [
            'title.required'       => 'A post title is required.',
            'category_id.exists'   => 'The selected category does not exist.',
            'cover.max'            => 'Cover image must be under 2 MB.',
        ];
    }

    // Custom attribute names
    public function attributes(): array
    {
        return [
            'category_id' => 'category',
            'published_at'=> 'publish date',
        ];
    }

    // Transform data before validation
    protected function prepareForValidation(): void
    {
        $this->merge([
            'title' => trim($this->title),
            'slug'  => \Str::slug($this->title),
        ]);
    }

    // After validation hook
    public function withValidator($validator): void
    {
        $validator->after(function ($validator) {
            if ($this->somethingElseIsInvalid()) {
                $validator->errors()->add('field', 'Something is wrong!');
            }
        });
    }
}

// In controller — just type-hint it, validation runs automatically
public function store(StorePostRequest $request): JsonResponse
{
    $data = $request->validated();  // Only validated data
    $post = Post::create($data);
    return response()->json($post, 201);
}
```

---

## 8. Middleware

### Creating Middleware

```php
// app/Http/Middleware/EnsureTokenIsValid.php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;

class EnsureTokenIsValid
{
    public function handle(Request $request, Closure $next): Response
    {
        // BEFORE request
        if ($request->input('token') !== 'my-secret-token') {
            return response()->json(['error' => 'Invalid token'], 401);
        }

        $response = $next($request);  // Pass to next middleware/controller

        // AFTER request
        $response->headers->set('X-Processed-By', 'MyMiddleware');

        return $response;
    }
}


// Middleware with parameters
class CheckRole
{
    public function handle(Request $request, Closure $next, string ...$roles): Response
    {
        if (! $request->user()->hasAnyRole($roles)) {
            abort(403, 'Unauthorized.');
        }
        return $next($request);
    }
}
// Usage: ->middleware('role:admin,editor')


// Terminable middleware (runs after response sent)
class LogRequest
{
    public function handle(Request $request, Closure $next): Response
    {
        return $next($request);
    }

    public function terminate(Request $request, Response $response): void
    {
        // Runs after response is sent to browser
        Log::info("Request to {$request->path()} returned {$response->getStatusCode()}");
    }
}
```

### Registering Middleware

```php
// bootstrap/app.php (Laravel 11+)
return Application::configure(basePath: dirname(__DIR__))
    ->withMiddleware(function (Middleware $middleware) {
        // Global middleware
        $middleware->append(EnsureTokenIsValid::class);
        $middleware->prepend(TrustProxies::class);

        // Middleware aliases
        $middleware->alias([
            'role'     => CheckRole::class,
            'throttle' => ThrottleRequests::class,
            'verified' => EnsureEmailIsVerified::class,
        ]);

        // Middleware groups
        $middleware->group('web', [
            \App\Http\Middleware\EncryptCookies::class,
            \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
            \Illuminate\Session\Middleware\StartSession::class,
            \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        ]);
    })
    ->create();


// Applying to routes
Route::get('/admin', AdminController::class)->middleware('auth');
Route::get('/admin', AdminController::class)->middleware(['auth', 'role:admin']);
Route::get('/admin', AdminController::class)->middleware('role:admin,editor');
Route::get('/admin', AdminController::class)->withoutMiddleware('auth');

// Excluding from group
Route::middleware('auth')->group(function () {
    Route::get('/login', LoginController::class)->withoutMiddleware('auth');
});
```

### Common Built-in Middleware

```php
'auth'              // Require authentication
'auth:api'          // Require API auth
'auth:sanctum'      // Require Sanctum token
'guest'             // Redirect if authenticated
'verified'          // Require email verification
'throttle:60,1'     // Rate limit: 60 req per 1 minute
'throttle:api'      // Use named throttle from config
'signed'            // Require signed URL
'password.confirm'  // Require password confirmation
'can:update,post'   // Policy gate check
'scope:read,write'  // Sanctum token scopes
```

---

## 9. Views & Blade Templates

### Basic View Rendering

```php
// Return a view from a controller
return view('posts.index');
return view('posts.index', ['posts' => $posts]);
return view('posts.index')->with('posts', $posts);
return view('posts.index', compact('posts', 'categories'));

// Check if view exists
if (view()->exists('emails.order')) { ... }
```

### Blade Syntax

```blade
{{-- resources/views/posts/index.blade.php --}}

{{-- Comments --}}
{{-- This is a Blade comment, won't appear in HTML --}}

{{-- Escaped output (XSS safe) --}}
{{ $name }}
{{ $name ?? 'Default' }}
{{ isset($name) ? $name : 'Default' }}

{{-- Unescaped output (use carefully!) --}}
{!! $htmlContent !!}

{{-- Blade directives --}}

{{-- If/else --}}
@if ($user->isAdmin())
    <p>You are an admin.</p>
@elseif ($user->isModerator())
    <p>You are a moderator.</p>
@else
    <p>Regular user.</p>
@endif

@unless ($user->isAdmin())
    <p>Not an admin.</p>
@endunless

{{-- Isset / empty --}}
@isset($variable)
    <p>{{ $variable }}</p>
@endisset

@empty($posts)
    <p>No posts found.</p>
@endempty

{{-- Loops --}}
@foreach ($posts as $post)
    <div>
        <h2>{{ $post->title }}</h2>
        <p>{{ $loop->index }}. Item</p>  {{-- Loop variable --}}
        @if ($loop->first) <hr> @endif
        @if ($loop->last) <hr> @endif
    </div>
@endforeach

@forelse ($posts as $post)
    <p>{{ $post->title }}</p>
@empty
    <p>No posts available.</p>
@endforelse

@for ($i = 0; $i < 10; $i++)
    <p>{{ $i }}</p>
@endfor

@while ($condition)
    <p>Running...</p>
@endwhile

{{-- Loop variable properties --}}
$loop->index       // 0-based index
$loop->iteration   // 1-based iteration
$loop->remaining   // Iterations remaining
$loop->count       // Total items
$loop->first       // Is first iteration
$loop->last        // Is last iteration
$loop->even        // Even iteration
$loop->odd         // Odd iteration
$loop->depth       // Nesting depth
$loop->parent      // Parent loop (nested)

{{-- Break & Continue --}}
@foreach ($posts as $post)
    @if ($post->status === 'deleted')
        @continue
    @endif
    @if ($loop->iteration === 5)
        @break
    @endif
    <p>{{ $post->title }}</p>
@endforeach

{{-- Switch --}}
@switch($role)
    @case('admin')
        <p>Admin panel</p>
        @break
    @case('editor')
        <p>Editor panel</p>
        @break
    @default
        <p>User panel</p>
@endswitch

{{-- PHP block --}}
@php
    $now = now();
    $greeting = $now->hour < 12 ? 'Good Morning' : 'Good Evening';
@endphp
<p>{{ $greeting }}</p>

{{-- Raw JS (avoid Blade parsing) --}}
@verbatim
    <div id="app">
        {{ message }}   {{-- This is Vue.js, not Blade --}}
    </div>
@endverbatim
```

### Template Inheritance

```blade
{{-- resources/views/layouts/app.blade.php --}}
<!DOCTYPE html>
<html>
<head>
    <title>@yield('title', 'My App')</title>
    @stack('styles')
</head>
<body>
    @include('layouts.partials.navbar')

    <main>
        @yield('content')
    </main>

    @include('layouts.partials.footer')
    @stack('scripts')
</body>
</html>


{{-- resources/views/posts/index.blade.php --}}
@extends('layouts.app')

@section('title', 'All Posts')

@push('styles')
    <link rel="stylesheet" href="{{ asset('css/posts.css') }}">
@endpush

@section('content')
    <h1>Posts</h1>

    @foreach ($posts as $post)
        @include('posts.partials.card', ['post' => $post])
    @endforeach
@endsection

@push('scripts')
    <script src="{{ asset('js/posts.js') }}"></script>
@endpush
```

### Blade Components

```bash
php artisan make:component Alert
php artisan make:component Forms/Input   # Namespaced
php artisan make:component Alert --view  # View-only (no class)
```

```php
// app/View/Components/Alert.php
namespace App\View\Components;

use Illuminate\View\Component;
use Illuminate\View\View;

class Alert extends Component
{
    public function __construct(
        public string $type = 'info',
        public string $message = '',
    ) {}

    public function render(): View
    {
        return view('components.alert');
    }

    // Computed property
    public function iconClass(): string
    {
        return match($this->type) {
            'success' => 'icon-check',
            'error'   => 'icon-x',
            default   => 'icon-info',
        };
    }
}
```

```blade
{{-- resources/views/components/alert.blade.php --}}
<div class="alert alert-{{ $type }}">
    <span class="{{ $iconClass() }}"></span>
    {{ $message }}
    {{ $slot }}    {{-- Default slot content --}}
</div>


{{-- Using the component --}}
<x-alert type="success" message="Saved!" />

<x-alert type="error">
    <strong>Error!</strong> Something went wrong.
</x-alert>

{{-- Anonymous component (no PHP class needed) --}}
{{-- resources/views/components/button.blade.php --}}
@props(['color' => 'blue', 'type' => 'button'])

<button type="{{ $type }}"
        class="btn btn-{{ $color }}"
        {{ $attributes->merge(['class' => 'font-bold']) }}>
    {{ $slot }}
</button>

{{-- Usage --}}
<x-button color="red" type="submit">Submit</x-button>
<x-button class="mt-4">Click me</x-button>


{{-- Named slots --}}
{{-- resources/views/components/card.blade.php --}}
<div class="card">
    <div class="card-header">{{ $header }}</div>
    <div class="card-body">{{ $slot }}</div>
    <div class="card-footer">{{ $footer ?? '' }}</div>
</div>

{{-- Usage --}}
<x-card>
    <x-slot name="header">Post Title</x-slot>
    Card body content here.
    <x-slot name="footer">Footer here</x-slot>
</x-card>
```

### Blade Directives

```blade
{{-- Authentication --}}
@auth
    <p>Logged in as {{ auth()->user()->name }}</p>
@endauth

@guest
    <a href="/login">Login</a>
@endguest

@auth('admin')
    <p>Admin user</p>
@endauth

{{-- Authorization --}}
@can('update', $post)
    <a href="/posts/{{ $post->id }}/edit">Edit</a>
@endcan

@cannot('delete', $post)
    <p>Cannot delete</p>
@endcannot

@canany(['update', 'delete'], $post)
    <p>Can update or delete</p>
@endcanany

{{-- Environment --}}
@production
    {{-- Only in production --}}
@endproduction

@env('staging')
    {{-- Only in staging --}}
@endenv

{{-- CSRF --}}
@csrf    {{-- Generates CSRF input field --}}

{{-- Method spoofing (for PUT/PATCH/DELETE forms) --}}
<form method="POST" action="/posts/1">
    @csrf
    @method('PUT')
    ...
</form>

{{-- Error display --}}
@error('title')
    <span class="error">{{ $message }}</span>
@enderror

{{-- Component class directives --}}
@includeIf('view.name', ['some' => 'data'])
@includeWhen($boolean, 'view.name', ['some' => 'data'])
@includeUnless($boolean, 'view.name', ['some' => 'data'])
@includeFirst(['custom.admin', 'admin'], ['some' => 'data'])

{{-- Assets --}}
{{ asset('images/logo.png') }}
{{ secure_asset('images/logo.png') }}

{{-- Vite assets --}}
@vite(['resources/css/app.css', 'resources/js/app.js'])
```

### Custom Blade Directives

```php
// app/Providers/AppServiceProvider.php
use Illuminate\Support\Facades\Blade;

public function boot(): void
{
    // Simple directive
    Blade::directive('money', function (string $expression) {
        return "<?php echo number_format($expression, 2) . ' USD'; ?>";
    });

    // If directive
    Blade::if('admin', function () {
        return auth()->check() && auth()->user()->isAdmin();
    });

    // Component alias
    Blade::component('alert', AlertComponent::class);
}

// Usage
@money($price)

@admin
    <a href="/admin">Admin Panel</a>
@endadmin
```

---

## 10. Models & Eloquent ORM

### Defining a Model

```php
// app/Models/Post.php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Casts\Attribute;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Illuminate\Database\Eloquent\Relations\HasMany;
use Illuminate\Database\Eloquent\Relations\BelongsToMany;

class Post extends Model
{
    use HasFactory, SoftDeletes;

    // Table name (default: snake_case plural of class name)
    protected $table = 'posts';

    // Primary key (default: 'id')
    protected $primaryKey = 'id';

    // If primary key is not auto-incrementing
    public $incrementing = false;
    protected $keyType = 'string'; // 'int' by default

    // Disable timestamps (created_at / updated_at)
    public $timestamps = false;

    // Custom timestamp column names
    const CREATED_AT = 'creation_date';
    const UPDATED_AT = 'last_update';

    // Mass assignable fields (whitelist)
    protected $fillable = [
        'title', 'slug', 'content', 'excerpt',
        'status', 'user_id', 'category_id',
        'cover_image', 'published_at',
    ];

    // Or blacklist (less secure — prefer fillable)
    protected $guarded = ['id'];

    // Hide from serialization (JSON/array)
    protected $hidden = ['password', 'remember_token', 'deleted_at'];

    // Always include in serialization
    protected $visible = ['id', 'name', 'email'];

    // Append custom accessor to JSON
    protected $appends = ['summary', 'author_name'];

    // Attribute casting
    protected $casts = [
        'is_featured'  => 'boolean',
        'view_count'   => 'integer',
        'price'        => 'decimal:2',
        'metadata'     => 'array',       // JSON column → PHP array
        'settings'     => 'collection',  // JSON column → Laravel Collection
        'published_at' => 'datetime',
        'options'      => 'encrypted',   // Encrypt/decrypt automatically
        'status'       => PostStatus::class,  // Enum cast (PHP 8.1+)
    ];

    // Date attributes
    protected $dates = ['published_at', 'deleted_at'];

    // Default attribute values
    protected $attributes = [
        'status'     => 'draft',
        'is_featured'=> false,
        'view_count' => 0,
    ];

    // ─── SCOPES ───────────────────────────────────────
    // Local scope
    public function scopePublished(Builder $query): void
    {
        $query->where('status', 'published');
    }

    public function scopeFeatured(Builder $query): void
    {
        $query->where('is_featured', true);
    }

    // Dynamic scope (with parameters)
    public function scopeOfStatus(Builder $query, string $status): void
    {
        $query->where('status', $status);
    }

    public function scopeSearch(Builder $query, string $term): void
    {
        $query->where(function ($q) use ($term) {
            $q->where('title', 'like', "%$term%")
              ->orWhere('content', 'like', "%$term%");
        });
    }

    // ─── ACCESSORS (Computed properties) ──────────────
    // New way (Laravel 9+)
    protected function title(): Attribute
    {
        return Attribute::make(
            get: fn (string $value) => ucfirst($value),
            set: fn (string $value) => strtolower($value),
        );
    }

    protected function summary(): Attribute
    {
        return Attribute::make(
            get: fn () => \Str::limit($this->content, 150),
        )->shouldCache();
    }

    protected function authorName(): Attribute
    {
        return Attribute::make(
            get: fn () => $this->author->name ?? 'Unknown',
        )->shouldCache();
    }

    // Old way (still works)
    // public function getTitleAttribute($value) { return ucfirst($value); }
    // public function setTitleAttribute($value) { $this->attributes['title'] = strtolower($value); }

    // ─── RELATIONSHIPS ────────────────────────────────
    public function author(): BelongsTo
    {
        return $this->belongsTo(User::class, 'user_id');
    }

    public function category(): BelongsTo
    {
        return $this->belongsTo(Category::class);
    }

    public function comments(): HasMany
    {
        return $this->hasMany(Comment::class);
    }

    public function tags(): BelongsToMany
    {
        return $this->belongsToMany(Tag::class)
                    ->withTimestamps()
                    ->withPivot('order');
    }

    // ─── LIFECYCLE HOOKS ─────────────────────────────
    protected static function booted(): void
    {
        static::creating(function (Post $post) {
            $post->slug = $post->slug ?? \Str::slug($post->title);
            $post->user_id = $post->user_id ?? auth()->id();
        });

        static::updating(function (Post $post) {
            if ($post->isDirty('title')) {
                $post->slug = \Str::slug($post->title);
            }
        });

        static::deleting(function (Post $post) {
            $post->tags()->detach();  // Clean up pivot table
        });
    }
}
```

### Complete Eloquent Query Reference

```php
// ─── RETRIEVING ───────────────────────────────────────
Post::all();
Post::all(['id', 'title']);               // Specific columns

Post::find(1);                            // By PK, null if not found
Post::find([1, 2, 3]);                    // Multiple PKs → Collection
Post::findOrFail(1);                      // 404 if not found
Post::findOr(1, fn() => abort(404));      // Custom callback

Post::first();
Post::firstOrFail();
Post::firstOr(fn() => new Post());

// ─── FILTERING ────────────────────────────────────────
Post::where('status', 'published')->get();
Post::where('status', '=', 'published')->get();  // Explicit operator
Post::where('view_count', '>', 100)->get();
Post::where('title', 'like', '%laravel%')->get();
Post::whereIn('status', ['draft', 'published'])->get();
Post::whereNotIn('status', ['archived'])->get();
Post::whereNull('deleted_at')->get();
Post::whereNotNull('published_at')->get();
Post::whereBetween('view_count', [100, 1000])->get();
Post::whereNotBetween('created_at', [$start, $end])->get();
Post::whereDate('created_at', '2024-01-01')->get();
Post::whereYear('created_at', 2024)->get();
Post::whereMonth('created_at', 6)->get();
Post::whereDay('created_at', 15)->get();
Post::whereTime('created_at', '09:00:00')->get();
Post::whereColumn('updated_at', '>', 'created_at')->get();

// Multiple wheres (AND)
Post::where('status', 'published')
    ->where('is_featured', true)
    ->where('view_count', '>', 100)
    ->get();

// OR
Post::where('status', 'published')
    ->orWhere('is_featured', true)
    ->get();

// Grouped OR (important for correct SQL precedence)
Post::where('status', 'published')
    ->where(function ($query) {
        $query->where('category_id', 1)
              ->orWhere('is_featured', true);
    })
    ->get();

// ─── ORDERING ─────────────────────────────────────────
Post::orderBy('created_at', 'desc')->get();
Post::orderByDesc('created_at')->get();
Post::orderByAsc('title')->get();
Post::inRandomOrder()->first();
Post::latest()->get();                    // orderBy created_at desc
Post::latest('published_at')->get();
Post::oldest()->get();                    // orderBy created_at asc

// ─── LIMITING ─────────────────────────────────────────
Post::take(10)->get();
Post::limit(10)->offset(20)->get();
Post::skip(20)->take(10)->get();

// ─── AGGREGATES ───────────────────────────────────────
Post::count();
Post::where('status', 'published')->count();
Post::max('view_count');
Post::min('view_count');
Post::avg('view_count');
Post::sum('view_count');

// ─── SELECTING COLUMNS ────────────────────────────────
Post::select('id', 'title', 'status')->get();
Post::select('posts.*', 'users.name as author_name')
    ->join('users', 'users.id', '=', 'posts.user_id')
    ->get();
Post::addSelect('metadata')->find(1);     // Add to existing select

// ─── DISTINCT ─────────────────────────────────────────
Post::select('category_id')->distinct()->get();

// ─── CHUNKS (for large datasets) ──────────────────────
Post::chunk(100, function ($posts) {
    foreach ($posts as $post) {
        // Process each post
    }
});

// Chunk by ID (safer with updates)
Post::chunkById(100, function ($posts) {
    $posts->each(fn($p) => $p->touch());
});

// Lazy loading (memory efficient)
foreach (Post::lazy() as $post) {
    // One at a time
}

Post::lazy()->each(function ($post) {
    // Process
});

// ─── SCOPES ───────────────────────────────────────────
Post::published()->get();
Post::published()->featured()->get();
Post::ofStatus('draft')->get();
Post::search('laravel')->latest()->paginate(15);

// ─── CREATING ─────────────────────────────────────────
$post = Post::create([
    'title'   => 'Hello World',
    'content' => 'Some content',
    'user_id' => 1,
]);

// Get or create
$tag = Tag::firstOrCreate(['name' => 'laravel'], ['color' => 'red']);
$tag = Tag::firstOrNew(['name' => 'laravel']);   // Not persisted yet
$tag->save();

// Update or create
$post = Post::updateOrCreate(
    ['slug' => 'hello-world'],            // Find by
    ['title' => 'Hello World', 'content' => '...'] // Update/set these
);

// ─── UPDATING ─────────────────────────────────────────
$post = Post::find(1);
$post->title = 'Updated Title';
$post->save();

$post->update(['title' => 'Updated', 'status' => 'published']);
$post->fill(['title' => 'Filled'])->save();

// Mass update (no model events fired!)
Post::where('status', 'draft')->update(['status' => 'archived']);

// Increment / Decrement
Post::find(1)->increment('view_count');
Post::find(1)->increment('view_count', 5);
Post::find(1)->decrement('likes_count');
Post::find(1)->increment('view_count', 1, ['updated_at' => now()]);

// Timestamps
$post->touch();                           // Update updated_at
$post->touch('published_at');

// ─── DELETING ─────────────────────────────────────────
$post = Post::find(1);
$post->delete();

Post::destroy(1);
Post::destroy([1, 2, 3]);
Post::where('status', 'draft')->delete();

// Soft delete (with SoftDeletes trait)
$post->delete();                          // Sets deleted_at
$post->restore();                         // Restore soft-deleted
$post->forceDelete();                     // Permanently delete

Post::withTrashed()->get();               // Include soft-deleted
Post::onlyTrashed()->get();               // Only soft-deleted
Post::withTrashed()->where('id', 1)->restore();

// ─── REFRESH & REPLICATE ──────────────────────────────
$fresh = $post->fresh();                  // New instance from DB
$post->refresh();                         // Reload in place
$clone = $post->replicate();             // Clone the model
$clone->title = 'Copy of ' . $post->title;
$clone->save();

// ─── CHECKING ─────────────────────────────────────────
$post->isDirty();                         // Has unsaved changes
$post->isDirty('title');                  // Specific attribute
$post->isClean();                         // No unsaved changes
$post->wasChanged();                      // Changed after last save
$post->wasChanged('title');
$post->getOriginal('title');              // Value before change
$post->getChanges();                      // All changes after save
$post->getDirty();                        // All pending changes

// ─── PAGINATION ───────────────────────────────────────
$posts = Post::published()->paginate(15);
$posts = Post::published()->paginate(15, ['*'], 'page', 2);  // Custom page
$posts = Post::published()->simplePaginate(15);  // No count (faster)
$posts = Post::published()->cursorPaginate(15);  // Cursor-based

// In template
{{ $posts->links() }}                     // Default pagination view
{{ $posts->links('vendor.pagination.bootstrap-5') }}

// Response
return response()->json([
    'data' => PostResource::collection($posts),
    'meta' => [
        'total'       => $posts->total(),
        'per_page'    => $posts->perPage(),
        'current_page'=> $posts->currentPage(),
        'last_page'   => $posts->lastPage(),
        'from'        => $posts->firstItem(),
        'to'          => $posts->lastItem(),
    ],
    'links' => [
        'next' => $posts->nextPageUrl(),
        'prev' => $posts->previousPageUrl(),
    ],
]);
```

### API Resources

```php
// app/Http/Resources/PostResource.php
namespace App\Http\Resources;

use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\JsonResource;

class PostResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        return [
            'id'         => $this->id,
            'title'      => $this->title,
            'slug'       => $this->slug,
            'excerpt'    => $this->excerpt,
            'content'    => $this->when(
                $request->routeIs('posts.show'),  // Only in detail view
                $this->content
            ),
            'status'     => $this->status,
            'is_featured'=> $this->is_featured,
            'view_count' => $this->view_count,
            'cover_url'  => $this->cover_image
                ? asset('storage/' . $this->cover_image)
                : null,

            // Conditional relationships
            'author'     => new UserResource($this->whenLoaded('author')),
            'category'   => new CategoryResource($this->whenLoaded('category')),
            'tags'       => TagResource::collection($this->whenLoaded('tags')),
            'comments'   => CommentResource::collection($this->whenLoaded('comments')),

            // Pivot data
            'order'      => $this->whenPivotLoaded('post_tag', fn() => $this->pivot->order),

            // Merge arrays
            $this->mergeWhen($request->user()?->isAdmin(), [
                'internal_notes' => $this->notes,
                'ip_address'     => $this->ip,
            ]),

            // Timestamps
            'created_at' => $this->created_at?->toISOString(),
            'updated_at' => $this->updated_at?->toISOString(),

            // URL
            'url'        => route('posts.show', $this->id),
        ];
    }

    // Customize the outer wrapper key
    public static $wrap = 'post';

    // Add extra meta to response
    public function with(Request $request): array
    {
        return [
            'meta' => [
                'api_version' => '1.0',
            ],
        ];
    }
}


// app/Http/Resources/PostCollection.php
namespace App\Http\Resources;

use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\ResourceCollection;

class PostCollection extends ResourceCollection
{
    public $collects = PostResource::class;

    public function toArray(Request $request): array
    {
        return [
            'data' => $this->collection,
        ];
    }

    public function with(Request $request): array
    {
        return [
            'meta' => [
                'total'       => $this->total(),
                'per_page'    => $this->perPage(),
                'current_page'=> $this->currentPage(),
            ],
        ];
    }
}

// Usage
return new PostResource($post);
return PostResource::collection($posts);
return new PostCollection($posts);       // Use for pagination meta
```

### Model Observers

```php
// app/Observers/PostObserver.php
namespace App\Observers;

use App\Models\Post;

class PostObserver
{
    public function creating(Post $post): void
    {
        $post->slug = $post->slug ?? \Str::slug($post->title);
    }

    public function created(Post $post): void
    {
        // Send notification, update cache, etc.
        cache()->forget('popular_posts');
    }

    public function updating(Post $post): void
    {
        if ($post->isDirty('status') && $post->status === 'published') {
            $post->published_at = now();
        }
    }

    public function updated(Post $post): void { }
    public function saving(Post $post): void { }    // before create or update
    public function saved(Post $post): void { }     // after create or update
    public function deleting(Post $post): void { }
    public function deleted(Post $post): void { }
    public function restoring(Post $post): void { } // SoftDeletes
    public function restored(Post $post): void { }  // SoftDeletes
    public function forceDeleting(Post $post): void { }
    public function forceDeleted(Post $post): void { }
}

// Register in AppServiceProvider
public function boot(): void
{
    Post::observe(PostObserver::class);
}
// Or in the model:
// protected static $observers = [PostObserver::class];
```

---

## 11. Migrations

### Creating Migrations

```bash
php artisan make:migration create_posts_table
php artisan make:migration add_published_at_to_posts_table
php artisan make:migration create_post_tag_table
```

### Migration File Structure

```php
// database/migrations/2024_01_01_000000_create_posts_table.php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('posts', function (Blueprint $table) {
            // ─── Primary Keys ───────────────────────
            $table->id();                              // BIGINT UNSIGNED AUTO_INCREMENT
            $table->bigIncrements('id');               // Same as above
            $table->uuid('id')->primary();             // UUID primary key
            $table->ulid('id')->primary();             // ULID primary key

            // ─── String Types ───────────────────────
            $table->string('title', 200);              // VARCHAR(200)
            $table->string('slug', 200)->unique();
            $table->text('content');                   // TEXT
            $table->mediumText('excerpt');             // MEDIUMTEXT
            $table->longText('full_content');          // LONGTEXT
            $table->char('code', 4);                   // CHAR(4)
            $table->tinyText('note');                  // TINYTEXT

            // ─── Number Types ───────────────────────
            $table->integer('view_count')->default(0);
            $table->unsignedInteger('likes');          // No negatives
            $table->tinyInteger('rating');             // -128 to 127
            $table->smallInteger('score');
            $table->mediumInteger('hits');
            $table->bigInteger('impressions');
            $table->unsignedBigInteger('user_id');     // For FK
            $table->float('rating', 2);                // FLOAT
            $table->double('price', 8, 2);             // DOUBLE
            $table->decimal('price', 10, 2);           // DECIMAL(10,2)

            // ─── Boolean ────────────────────────────
            $table->boolean('is_featured')->default(false);

            // ─── Date/Time ──────────────────────────
            $table->date('birthday');
            $table->time('start_time');
            $table->dateTime('scheduled_at');
            $table->timestamp('published_at')->nullable();
            $table->timestamps();                      // created_at + updated_at
            $table->softDeletes();                     // deleted_at
            $table->softDeletesTz();                   // With timezone
            $table->timestampsTz();                    // With timezone
            $table->year('graduation_year');

            // ─── JSON ───────────────────────────────
            $table->json('metadata')->nullable();
            $table->jsonb('settings')->nullable();     // PostgreSQL only

            // ─── Enum ───────────────────────────────
            $table->enum('status', ['draft', 'published', 'archived'])->default('draft');

            // ─── Binary ─────────────────────────────
            $table->binary('data');

            // ─── IP / MAC ───────────────────────────
            $table->ipAddress('visitor_ip');
            $table->macAddress('device_mac');

            // ─── UUID ───────────────────────────────
            $table->uuid('uuid')->unique()->nullable();
            $table->ulid('ulid')->unique()->nullable();

            // ─── Foreign Keys ───────────────────────
            $table->foreignId('user_id')
                  ->constrained()                      // → users.id
                  ->onDelete('cascade');

            $table->foreignId('category_id')
                  ->nullable()
                  ->constrained()
                  ->nullOnDelete();

            $table->foreignId('author_id')
                  ->constrained('users')               // Explicit table
                  ->onDelete('restrict');

            // Explicit FK
            $table->unsignedBigInteger('parent_id')->nullable();
            $table->foreign('parent_id')->references('id')->on('posts')->onDelete('cascade');

            // ─── Indexes ────────────────────────────
            $table->index('status');
            $table->index(['status', 'created_at']);   // Composite
            $table->unique('slug');
            $table->unique(['user_id', 'slug']);
            $table->fullText('content');               // MySQL/PostgreSQL
            $table->spatialIndex('location');          // MySQL
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('posts');
    }
};


// Modifying existing table
Schema::table('posts', function (Blueprint $table) {
    // Add column
    $table->string('summary', 500)->nullable()->after('content');
    $table->boolean('is_pinned')->default(false);

    // Modify column (needs doctrine/dbal)
    $table->string('title', 300)->change();
    $table->string('content')->nullable()->change();

    // Rename column
    $table->renameColumn('body', 'content');

    // Drop column
    $table->dropColumn('old_column');
    $table->dropColumn(['col1', 'col2']);

    // Add index
    $table->index('status');
    $table->unique(['email', 'deleted_at']);

    // Drop index
    $table->dropIndex('posts_status_index');
    $table->dropUnique('posts_email_unique');
    $table->dropPrimary();

    // Add FK
    $table->foreign('tag_id')->references('id')->on('tags')->onDelete('cascade');
    $table->dropForeign(['tag_id']);
    $table->dropForeign('posts_tag_id_foreign');

    // Drop timestamps
    $table->dropTimestamps();
    $table->dropSoftDeletes();
});
```

### Pivot/Junction Table Migration

```php
Schema::create('post_tag', function (Blueprint $table) {
    $table->foreignId('post_id')->constrained()->onDelete('cascade');
    $table->foreignId('tag_id')->constrained()->onDelete('cascade');
    $table->primary(['post_id', 'tag_id']);    // Composite PK
    $table->integer('order')->default(0);
    $table->timestamps();
});
```

---

## 12. Seeders & Factories

### Factories

```php
// database/factories/PostFactory.php
namespace Database\Factories;

use App\Models\Post;
use App\Models\User;
use App\Models\Category;
use Illuminate\Database\Eloquent\Factories\Factory;
use Illuminate\Support\Str;

class PostFactory extends Factory
{
    protected $model = Post::class;

    public function definition(): array
    {
        $title = fake()->sentence(6);

        return [
            'title'        => $title,
            'slug'         => Str::slug($title),
            'content'      => fake()->paragraphs(5, true),
            'excerpt'      => fake()->paragraph(),
            'status'       => fake()->randomElement(['draft', 'published']),
            'is_featured'  => fake()->boolean(20),  // 20% chance true
            'view_count'   => fake()->numberBetween(0, 10000),
            'user_id'      => User::factory(),      // Creates a User
            'category_id'  => Category::factory(),  // Creates a Category
            'published_at' => fake()->optional(0.7)->dateTimeThisYear(),
            'created_at'   => fake()->dateTimeThisYear(),
        ];
    }

    // States — predefined states
    public function published(): static
    {
        return $this->state(fn(array $attrs) => [
            'status'       => 'published',
            'published_at' => now(),
        ]);
    }

    public function draft(): static
    {
        return $this->state(fn(array $attrs) => [
            'status'       => 'draft',
            'published_at' => null,
        ]);
    }

    public function featured(): static
    {
        return $this->state(fn(array $attrs) => [
            'is_featured' => true,
        ]);
    }

    public function forUser(User $user): static
    {
        return $this->state(fn(array $attrs) => [
            'user_id' => $user->id,
        ]);
    }

    // Sequence — alternate values
    public function withAlternatingStatus(): static
    {
        return $this->sequence(
            ['status' => 'draft'],
            ['status' => 'published'],
        );
    }

    // After creating
    public function configure(): static
    {
        return $this->afterCreating(function (Post $post) {
            $post->tags()->attach(
                Tag::factory()->count(3)->create()
            );
        });
    }
}


// Using factories
// Create and persist
$post = Post::factory()->create();
$post = Post::factory()->published()->featured()->create();
$post = Post::factory()->forUser($user)->create(['title' => 'Custom Title']);
$posts = Post::factory()->count(10)->create();
$posts = Post::factory()->count(10)->published()->create();

// Create without persisting
$post = Post::factory()->make();
$posts = Post::factory()->count(5)->make();

// Sequences
$posts = Post::factory()
    ->count(6)
    ->sequence(
        ['status' => 'published'],
        ['status' => 'draft'],
    )
    ->create();
```

### Seeders

```php
// database/seeders/PostSeeder.php
namespace Database\Seeders;

use App\Models\Post;
use App\Models\User;
use App\Models\Tag;
use Illuminate\Database\Seeder;

class PostSeeder extends Seeder
{
    public function run(): void
    {
        // Create specific users first
        $adminUser = User::factory()->create([
            'email' => 'admin@example.com',
            'name'  => 'Admin User',
        ]);

        // Create tags
        $tags = Tag::factory()->count(10)->create();

        // Create posts with relationships
        Post::factory()
            ->count(50)
            ->published()
            ->forUser($adminUser)
            ->create()
            ->each(function (Post $post) use ($tags) {
                $post->tags()->attach(
                    $tags->random(rand(1, 4))->pluck('id')
                );
            });

        // Create draft posts
        Post::factory()->count(10)->draft()->create();

        // Featured posts
        Post::factory()->count(5)->featured()->published()->create();

        $this->command->info('Posts seeded successfully!');
    }
}


// database/seeders/DatabaseSeeder.php
class DatabaseSeeder extends Seeder
{
    public function run(): void
    {
        $this->call([
            UserSeeder::class,
            CategorySeeder::class,
            TagSeeder::class,
            PostSeeder::class,
            CommentSeeder::class,
        ]);
    }
}

// Run
// php artisan db:seed
// php artisan db:seed --class=PostSeeder
// php artisan migrate:fresh --seed
```

---

## 13. Eloquent Relationships

### All Relationship Types

```php
// ─── ONE TO ONE ──────────────────────────────────────
// User has one Profile
class User extends Model
{
    public function profile(): HasOne
    {
        return $this->hasOne(Profile::class);
        // Laravel assumes FK: profiles.user_id
        // Custom: return $this->hasOne(Profile::class, 'foreign_key', 'local_key');
    }
}

class Profile extends Model
{
    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class);
    }
}

// Usage
$profile = User::find(1)->profile;
$user = Profile::find(1)->user;
$user->profile()->create(['bio' => 'Hello!']);


// ─── ONE TO MANY ──────────────────────────────────────
class Post extends Model
{
    public function comments(): HasMany
    {
        return $this->hasMany(Comment::class)
                    ->orderBy('created_at', 'desc');
    }

    // Latest comment shortcut
    public function latestComment(): HasOne
    {
        return $this->hasOne(Comment::class)->latestOfMany();
    }

    public function oldestComment(): HasOne
    {
        return $this->hasOne(Comment::class)->oldestOfMany();
    }

    public function topComment(): HasOne
    {
        return $this->hasOne(Comment::class)->ofMany('likes_count', 'max');
    }
}

class Comment extends Model
{
    public function post(): BelongsTo
    {
        return $this->belongsTo(Post::class);
    }
}

// Usage
$comments = Post::find(1)->comments;
$comments = Post::find(1)->comments()->where('is_approved', true)->get();
$post->comments()->create(['content' => 'Great post!', 'user_id' => 1]);


// ─── MANY TO MANY ─────────────────────────────────────
class Post extends Model
{
    public function tags(): BelongsToMany
    {
        return $this->belongsToMany(Tag::class)
                    ->using(PostTag::class)           // Custom pivot model
                    ->withPivot('order', 'added_by')  // Include pivot columns
                    ->withTimestamps()                // Pivot timestamps
                    ->orderByPivot('order');
    }

    public function approvedTags(): BelongsToMany
    {
        return $this->belongsToMany(Tag::class)
                    ->wherePivot('is_approved', true);
    }
}

class Tag extends Model
{
    public function posts(): BelongsToMany
    {
        return $this->belongsToMany(Post::class);
    }
}

// Pivot model
class PostTag extends \Illuminate\Database\Eloquent\Relations\Pivot
{
    protected $casts = ['added_at' => 'datetime'];
}

// Usage
$tags = Post::find(1)->tags;
$tagIds = [1, 2, 3];

// Attach / Detach
$post->tags()->attach(1);
$post->tags()->attach([1, 2, 3]);
$post->tags()->attach(1, ['order' => 1]);          // With pivot data
$post->tags()->detach(1);
$post->tags()->detach([1, 2]);
$post->tags()->detach();                           // Remove all

// Sync (attach + detach to match exactly)
$post->tags()->sync([1, 2, 3]);
$post->tags()->sync([1 => ['order' => 1], 2 => ['order' => 2]]);
$post->tags()->syncWithoutDetaching([1, 2]);       // Only attach, no detach
$post->tags()->syncWithPivotValues([1, 2], ['added_by' => auth()->id()]);

// Toggle
$post->tags()->toggle([1, 2, 3]);                  // Attach if not attached, detach if attached

// Pivot access
foreach ($post->tags as $tag) {
    echo $tag->pivot->order;
    echo $tag->pivot->created_at;
}

// Update pivot
$post->tags()->updateExistingPivot(1, ['order' => 5]);


// ─── HAS MANY THROUGH ─────────────────────────────────
// User → Posts → Comments (User's comments through posts)
class User extends Model
{
    public function postComments(): HasManyThrough
    {
        return $this->hasManyThrough(Comment::class, Post::class);
        // Has many Comments through Posts
    }
}

// Country → Users → Posts (Country's posts through users)
class Country extends Model
{
    public function posts(): HasManyThrough
    {
        return $this->hasManyThrough(
            Post::class,        // Final model
            User::class,        // Through model
            'country_id',       // FK on users
            'user_id',          // FK on posts
            'id',               // Local key on countries
            'id',               // Local key on users
        );
    }
}


// ─── POLYMORPHIC RELATIONS ────────────────────────────
// Comment can belong to Post OR Video
class Comment extends Model
{
    public function commentable(): MorphTo
    {
        return $this->morphTo();
        // Uses: commentable_type, commentable_id columns
    }
}

class Post extends Model
{
    public function comments(): MorphMany
    {
        return $this->morphMany(Comment::class, 'commentable');
    }
}

class Video extends Model
{
    public function comments(): MorphMany
    {
        return $this->morphMany(Comment::class, 'commentable');
    }
}

// Usage
$comments = Post::find(1)->comments;
$post = Comment::find(1)->commentable;  // Returns a Post or Video

// Create polymorphic
$post->comments()->create(['content' => '...', 'user_id' => 1]);


// MorphOne
class User extends Model
{
    public function image(): MorphOne
    {
        return $this->morphOne(Image::class, 'imageable');
    }
}


// MorphToMany (polymorphic many-to-many)
// Tag can be applied to Post or Video
class Post extends Model
{
    public function tags(): MorphToMany
    {
        return $this->morphToMany(Tag::class, 'taggable');
    }
}

class Tag extends Model
{
    public function posts(): MorphedByMany
    {
        return $this->morphedByMany(Post::class, 'taggable');
    }

    public function videos(): MorphedByMany
    {
        return $this->morphedByMany(Video::class, 'taggable');
    }
}
```

### Eager Loading

```php
// N+1 Problem — BAD
$posts = Post::all();
foreach ($posts as $post) {
    echo $post->author->name;  // 1 query per post!
}

// Eager loading — GOOD (1 + 1 queries)
$posts = Post::with('author')->get();

// Multiple relations
$posts = Post::with(['author', 'category', 'tags'])->get();

// Nested relations
$posts = Post::with('author.profile')->get();
$posts = Post::with(['comments' => function ($query) {
    $query->where('is_approved', true)->with('author');
}])->get();

// Lazy eager loading (after already fetching)
$posts = Post::all();
$posts->load('author');
$posts->load(['author', 'tags']);
$posts->loadMissing('author');              // Only if not already loaded

// Counting relations (without loading)
$posts = Post::withCount('comments')->get();
foreach ($posts as $post) {
    echo $post->comments_count;
}

// WithCount + constraint
$posts = Post::withCount([
    'comments',
    'comments as approved_comments_count' => fn($q) => $q->where('is_approved', true),
])->get();

// WithSum, WithAvg, WithMax, WithMin
$posts = Post::withSum('comments', 'likes_count')->get();
$posts = Post::withAvg('ratings', 'score')->get();

// Prevent lazy loading (N+1 protection)
// In AppServiceProvider boot():
Model::preventLazyLoading(! app()->isProduction());
```

---

## 14. Query Builder

The Query Builder lets you write raw SQL queries fluently without Eloquent.

```php
use Illuminate\Support\Facades\DB;

// ─── SELECT ───────────────────────────────────────────
DB::table('posts')->get();
DB::table('posts')->first();
DB::table('posts')->find(1);
DB::table('posts')->value('title');                     // Single value
DB::table('posts')->pluck('title');                     // Collection of titles
DB::table('posts')->pluck('title', 'id');               // Keyed by id

DB::table('posts')->select('id', 'title', 'status')->get();
DB::table('posts')
    ->select('posts.*', DB::raw('COUNT(comments.id) as comment_count'))
    ->leftJoin('comments', 'comments.post_id', '=', 'posts.id')
    ->groupBy('posts.id')
    ->get();

// ─── WHERE ────────────────────────────────────────────
DB::table('posts')
    ->where('status', 'published')
    ->where('view_count', '>', 100)
    ->orWhere('is_featured', true)
    ->whereIn('category_id', [1, 2, 3])
    ->whereNotIn('status', ['deleted'])
    ->whereNull('deleted_at')
    ->whereNotNull('published_at')
    ->whereBetween('view_count', [100, 1000])
    ->whereDate('created_at', '2024-01-01')
    ->whereYear('created_at', '2024')
    ->get();

// Raw where
DB::table('posts')
    ->whereRaw('view_count > ? and status = ?', [100, 'published'])
    ->get();

// ─── JOINS ────────────────────────────────────────────
DB::table('posts')
    ->join('users', 'users.id', '=', 'posts.user_id')
    ->leftJoin('categories', 'categories.id', '=', 'posts.category_id')
    ->rightJoin('comments', 'comments.post_id', '=', 'posts.id')
    ->crossJoin('tags')
    ->select('posts.*', 'users.name as author')
    ->get();

// Advanced join
DB::table('posts')
    ->join('comments', function ($join) {
        $join->on('comments.post_id', '=', 'posts.id')
             ->where('comments.is_approved', '=', 1);
    })
    ->get();

// ─── AGGREGATES ───────────────────────────────────────
DB::table('posts')->count();
DB::table('posts')->where('status', 'published')->count();
DB::table('posts')->max('view_count');
DB::table('posts')->min('view_count');
DB::table('posts')->avg('view_count');
DB::table('posts')->sum('view_count');

// ─── GROUP BY / HAVING ────────────────────────────────
DB::table('posts')
    ->select('category_id', DB::raw('COUNT(*) as post_count'))
    ->groupBy('category_id')
    ->having('post_count', '>', 5)
    ->get();

// ─── ORDER / LIMIT ────────────────────────────────────
DB::table('posts')
    ->orderBy('created_at', 'desc')
    ->orderByRaw('FIELD(status, "published", "draft", "archived")')
    ->inRandomOrder()
    ->limit(10)
    ->offset(20)
    ->get();

// ─── INSERT ───────────────────────────────────────────
DB::table('posts')->insert([
    'title'      => 'Hello World',
    'created_at' => now(),
]);

// Insert multiple
DB::table('posts')->insert([
    ['title' => 'Post 1', 'user_id' => 1],
    ['title' => 'Post 2', 'user_id' => 1],
]);

// Insert and get ID
$id = DB::table('posts')->insertGetId([
    'title' => 'Hello World',
]);

// Insert or ignore (duplicate key)
DB::table('posts')->insertOrIgnore([...]);

// Upsert (insert or update)
DB::table('posts')->upsert(
    [['id' => 1, 'title' => 'Updated', 'status' => 'published']],
    ['id'],                // Columns to check for conflicts
    ['title', 'status'],   // Columns to update on conflict
);

// ─── UPDATE ───────────────────────────────────────────
DB::table('posts')->where('id', 1)->update(['title' => 'Updated']);
DB::table('posts')->where('status', 'draft')->update(['status' => 'archived']);
DB::table('posts')->increment('view_count');
DB::table('posts')->increment('view_count', 5);
DB::table('posts')->decrement('likes_count');
DB::table('posts')->increment('view_count', 1, ['updated_at' => now()]);
DB::table('posts')->updateOrInsert(
    ['email' => 'john@example.com'],  // Search condition
    ['name' => 'John Doe']            // Update/insert data
);

// ─── DELETE ───────────────────────────────────────────
DB::table('posts')->delete();
DB::table('posts')->where('id', 1)->delete();
DB::table('posts')->where('status', 'archived')->delete();
DB::table('posts')->truncate();             // Delete all + reset auto-increment

// ─── TRANSACTIONS ─────────────────────────────────────
DB::transaction(function () {
    DB::table('users')->update(['votes' => 1]);
    DB::table('posts')->delete();
});

// Manual transaction
DB::beginTransaction();
try {
    DB::table('users')->update([...]);
    DB::table('posts')->insert([...]);
    DB::commit();
} catch (\Throwable $e) {
    DB::rollBack();
    throw $e;
}

// ─── RAW EXPRESSIONS ──────────────────────────────────
DB::table('posts')
    ->select(DB::raw('COUNT(*) as total, status'))
    ->groupBy('status')
    ->get();

DB::table('posts')->whereRaw('LENGTH(title) > ?', [20])->get();
DB::table('posts')->orderByRaw('RAND()')->get();
DB::table('posts')->groupByRaw('DATE(created_at)')->get();

// ─── SUBQUERIES ───────────────────────────────────────
$latestPost = DB::table('posts')
    ->select('user_id', DB::raw('MAX(created_at) as last_post_at'))
    ->where('status', 'published')
    ->groupBy('user_id');

DB::table('users')
    ->joinSub($latestPost, 'latest_post', fn($join) => (
        $join->on('users.id', '=', 'latest_post.user_id')
    ))
    ->get();

// ─── FULL-TEXT SEARCH ─────────────────────────────────
DB::table('posts')
    ->whereFullText(['title', 'content'], 'Laravel framework')
    ->get();
```

---

## 15. Collections

Laravel Collections are a fluent wrapper around arrays with many powerful methods.

```php
use Illuminate\Support\Collection;

$collection = collect([1, 2, 3, 4, 5]);
$posts = Post::all();  // Eloquent always returns Collections

// ─── TRANSFORMING ────────────────────────────────────
$doubled = $collection->map(fn($item) => $item * 2);
// [2, 4, 6, 8, 10]

$filtered = $collection->filter(fn($item) => $item > 2);
// [3, 4, 5]

$flattened = collect([[1, 2], [3, 4]])->flatten();
// [1, 2, 3, 4]

$grouped = collect([
    ['name' => 'Alice', 'role' => 'admin'],
    ['name' => 'Bob',   'role' => 'user'],
    ['name' => 'Carol', 'role' => 'admin'],
])->groupBy('role');
// { admin: [Alice, Carol], user: [Bob] }

$keyed = collect([
    ['id' => 1, 'name' => 'Alice'],
    ['id' => 2, 'name' => 'Bob'],
])->keyBy('id');
// { 1: {id:1, name:Alice}, 2: {id:2, name:Bob} }

$plucked = collect([
    ['name' => 'Alice', 'age' => 30],
    ['name' => 'Bob',   'age' => 25],
])->pluck('name');
// ['Alice', 'Bob']

$plucked = collect($posts)->pluck('title', 'id');
// [1 => 'Post 1', 2 => 'Post 2']

// ─── SEARCHING ────────────────────────────────────────
$first = $collection->first(fn($item) => $item > 3);   // 4
$last  = $collection->last(fn($item) => $item < 4);    // 3

$has   = $collection->contains(3);                     // true
$has   = $collection->contains(fn($item) => $item > 4);

$item  = $collection->search(3);                       // Returns key
$item  = $collection->firstWhere('status', 'published');

// ─── AGGREGATING ──────────────────────────────────────
$collection->count();
$collection->sum();
$collection->sum('price');
$collection->avg('view_count');
$collection->max('price');
$collection->min('price');

// ─── SORTING ──────────────────────────────────────────
$sorted  = $collection->sort();
$sorted  = $collection->sortDesc();
$sorted  = collect($posts)->sortBy('title');
$sorted  = collect($posts)->sortByDesc('created_at');
$sorted  = collect($posts)->sortBy([
    ['status', 'asc'],
    ['created_at', 'desc'],
]);

// ─── SLICING ──────────────────────────────────────────
$sliced  = $collection->take(3);
$sliced  = $collection->skip(2)->take(3);
$sliced  = $collection->slice(2, 3);                   // offset, length
$chunks  = $collection->chunk(3);                      // Break into chunks
$split   = $collection->split(3);                      // Split into N groups

// ─── COMBINING ────────────────────────────────────────
$merged  = $collection->merge([6, 7, 8]);
$pushed  = $collection->push(6);
$prepend = $collection->prepend(0);
$concat  = $collection->concat([6, 7]);
$zipped  = $collection->zip([6, 7, 8, 9, 10]);
$combined= collect(['a', 'b'])->combine([1, 2]);       // [a=>1, b=>2]

// ─── UNIQUE / DUPLICATE ───────────────────────────────
$unique  = $collection->unique();
$unique  = collect($posts)->unique('category_id');
$dupes   = $collection->duplicates();

// ─── MATH ─────────────────────────────────────────────
$reduced = $collection->reduce(fn($carry, $item) => $carry + $item, 0);

// ─── OUTPUT ───────────────────────────────────────────
$array  = $collection->toArray();
$json   = $collection->toJson();
$all    = $collection->all();
$values = $collection->values();    // Reset keys
$keys   = $collection->keys();

// ─── MISC ─────────────────────────────────────────────
$tapped  = $collection->tap(fn($c) => \Log::info($c->count()));  // Inspect without changing
$each    = $collection->each(fn($item) => processItem($item));
$whenTrue = $collection->when(true, fn($c) => $c->filter(...));
$unless  = $collection->unless(false, fn($c) => $c->filter(...));

$isEmpty = $collection->isEmpty();
$isNotEmpty = $collection->isNotEmpty();

$reversed = $collection->reverse();
$shuffled = $collection->shuffle();

// Lazy collections (memory efficient)
$lazy = LazyCollection::make(function () {
    $handle = fopen('bigfile.csv', 'r');
    while (($line = fgets($handle)) !== false) {
        yield $line;
    }
});

// Convert Eloquent to lazy
Post::cursor()->each(fn($post) => processPost($post));
```

---

## 16. Validation

### In-line Validation

```php
use Illuminate\Support\Facades\Validator;
use Illuminate\Validation\Rule;
use Illuminate\Validation\Rules\Password;

// In a controller
public function store(Request $request)
{
    $validated = $request->validate([
        'name'     => 'required|string|max:255',
        'email'    => 'required|email|unique:users,email',
        'password' => ['required', 'confirmed', Password::min(8)->mixedCase()->numbers()],
    ]);

    // Or use Validator facade
    $validator = Validator::make($request->all(), [
        'name'  => 'required|string|max:255',
        'email' => 'required|email',
    ]);

    if ($validator->fails()) {
        return response()->json(['errors' => $validator->errors()], 422);
    }

    $data = $validator->validated();
}
```

### All Validation Rules

```php
// ─── REQUIRED ─────────────────────────────────────────
'required'                          // Not null, not empty
'required_if:field,value'           // Required if field equals value
'required_unless:field,value'       // Required unless field equals value
'required_with:other_field'         // Required if other field is present
'required_without:other_field'      // Required if other field is absent
'required_with_all:a,b'             // Required if ALL listed present
'required_without_all:a,b'          // Required if ALL listed absent
'nullable'                          // Allow null

// ─── TYPE ─────────────────────────────────────────────
'string'
'integer' (or 'int')
'numeric'                           // Int or float
'boolean'                           // 1, 0, true, false, "true", "false"
'array'
'array:key1,key2'                   // Array with only these keys
'json'
'date'
'date_format:Y-m-d'
'email'
'email:rfc,dns'                     // Stricter email validation
'url'
'active_url'                        // URL must be active (DNS check)
'ip'
'ipv4'
'ipv6'
'uuid'
'ulid'
'alpha'
'alpha_num'
'alpha_dash'                        // Alpha, numbers, dashes, underscores

// ─── SIZE ─────────────────────────────────────────────
'min:3'                             // String: min chars. Int: min value. Array: min count.
'max:255'
'size:5'
'between:1,100'
'digits:5'                          // Exactly 5 digits
'digits_between:4,8'

// ─── STRING ───────────────────────────────────────────
'starts_with:foo,bar'
'ends_with:foo,bar'
'contains:foo,bar'
'doesnt_start_with:foo'
'doesnt_end_with:foo'
'regex:/^[A-Za-z]+$/'
'not_regex:/^bad/'
'lowercase'
'uppercase'
'ascii'
'hex_color'
'slug'

// ─── DATABASE ─────────────────────────────────────────
'unique:table,column'
'unique:table,column,ignore_id'     // Ignore own record on update
'unique:users,email,' . $user->id
Rule::unique('users', 'email')->ignore($user->id)
Rule::unique('users', 'email')->where(fn($q) => $q->where('active', 1))
'exists:table,column'
'exists:users,id'
Rule::exists('users', 'id')->where(fn($q) => $q->where('active', 1))

// ─── FILE ─────────────────────────────────────────────
'file'
'image'                             // jpg, jpeg, png, bmp, gif, svg, webp
'mimes:jpg,jpeg,png,pdf'
'mimetypes:image/jpeg,image/png'
'max:2048'                          // Max size in KB
'min:10'                            // Min size in KB
'dimensions:min_width=100,max_width=1000,ratio=3/2'
'extensions:jpg,png'

// ─── DATE ─────────────────────────────────────────────
'date'
'date_equals:2024-01-01'
'before:tomorrow'
'before_or_equal:today'
'after:yesterday'
'after_or_equal:today'
'after:start_date'                  // After the value of start_date field

// ─── COMPARISON ───────────────────────────────────────
'same:password'                     // Must match password field
'different:old_password'            // Must differ from old_password
'confirmed'                         // field_confirmation must match field
'in:draft,published,archived'
Rule::in(['draft', 'published'])
'not_in:deleted'
Rule::notIn(['deleted'])
'gt:field'                          // Greater than another field's value
'gte:field'
'lt:field'
'lte:field'

// ─── PASSWORD ─────────────────────────────────────────
Password::min(8)
Password::min(8)->letters()
Password::min(8)->mixedCase()
Password::min(8)->numbers()
Password::min(8)->symbols()
Password::min(8)->uncompromised()    // Check against HaveIBeenPwned
Password::defaults()                 // Use configured defaults

// ─── MISC ─────────────────────────────────────────────
'bail'                              // Stop validating after first failure
'sometimes'                         // Validate only if field is present
'prohibited'                        // Must be absent or empty
'prohibited_if:field,value'
'prohibited_unless:field,value'
'present'                           // Must be present (even if empty)
'filled'                            // If present, must not be empty
'accepted'                          // 'yes', 'on', 1, true
'declined'                          // 'no', 'off', 0, false
'accepted_if:terms,1'
```

### Custom Validation Rules

```php
// Rule class
php artisan make:rule Uppercase

// app/Rules/Uppercase.php
class Uppercase implements ValidationRule
{
    public function validate(string $attribute, mixed $value, Closure $fail): void
    {
        if (strtoupper($value) !== $value) {
            $fail("The :attribute must be uppercase.");
        }
    }
}

// Usage
'name' => ['required', new Uppercase],

// Implicit rule (validates even when field is absent)
class RequiredIfAdmin implements ImplicitRule { ... }

// Closure rule (inline)
'title' => [
    'required',
    function (string $attribute, mixed $value, Closure $fail) {
        if (str_contains($value, 'spam')) {
            $fail("The :attribute contains spam.");
        }
    },
],

// Rule::make() inline
'title' => [
    'required',
    Rule::when(
        $request->user()->isAdmin(),
        ['min:3'],
        ['min:10'],
    ),
],
```

---

## 17. Authentication

### Laravel Breeze (Starter Kit)

```bash
composer require laravel/breeze --dev
php artisan breeze:install
# Choose: blade, react, vue, api, livewire
php artisan migrate
npm install && npm run dev
```

### Manual Authentication

```php
use Illuminate\Support\Facades\Auth;

// Login
if (Auth::attempt(['email' => $email, 'password' => $password])) {
    $request->session()->regenerate();  // Prevent session fixation
    return redirect()->intended('/dashboard');
}

// Login with "remember me"
Auth::attempt(['email' => $email, 'password' => $password], $remember);

// Login a specific user (no password check)
Auth::login($user);
Auth::login($user, true);              // Remember
Auth::loginUsingId(1);
Auth::loginUsingId(1, true);

// Once (stateless — no session, just for current request)
Auth::once(['email' => $email, 'password' => $password]);
Auth::onceUsingId(1);

// Check
Auth::check();                          // Is logged in?
Auth::guest();                          // Is guest?
Auth::user();                           // Get user or null
Auth::id();                             // Get user ID or null
auth()->user();                         // Helper
auth()->id();

// Logout
Auth::logout();
$request->session()->invalidate();
$request->session()->regenerateToken();  // Regenerate CSRF token
return redirect('/');

// Get user in controller
$user = $request->user();

// Multiple guards
Auth::guard('admin')->check();
Auth::guard('api')->user();
```

### Auth Routes & Controllers

```php
// routes/web.php
Route::middleware('guest')->group(function () {
    Route::get('/register', [AuthController::class, 'showRegister'])->name('register');
    Route::post('/register', [AuthController::class, 'register']);
    Route::get('/login', [AuthController::class, 'showLogin'])->name('login');
    Route::post('/login', [AuthController::class, 'login']);
});

Route::middleware('auth')->group(function () {
    Route::post('/logout', [AuthController::class, 'logout'])->name('logout');
    Route::get('/dashboard', DashboardController::class)->name('dashboard');
});


// app/Http/Controllers/AuthController.php
class AuthController extends Controller
{
    public function register(StoreUserRequest $request): RedirectResponse
    {
        $user = User::create([
            'name'     => $request->name,
            'email'    => $request->email,
            'password' => Hash::make($request->password),
        ]);

        Auth::login($user);
        return redirect()->route('dashboard');
    }

    public function login(LoginRequest $request): RedirectResponse
    {
        if (! Auth::attempt($request->only('email', 'password'), $request->boolean('remember'))) {
            throw ValidationException::withMessages([
                'email' => [trans('auth.failed')],
            ]);
        }

        $request->session()->regenerate();
        return redirect()->intended(route('dashboard'));
    }

    public function logout(Request $request): RedirectResponse
    {
        Auth::logout();
        $request->session()->invalidate();
        $request->session()->regenerateToken();
        return redirect('/');
    }
}
```

### Email Verification

```php
// Implement MustVerifyEmail on User model
use Illuminate\Contracts\Auth\MustVerifyEmail;

class User extends Authenticatable implements MustVerifyEmail { ... }

// Routes
Route::middleware(['auth', 'verified'])->group(function () {
    Route::get('/dashboard', DashboardController::class);
});

// Send verification email
$user->sendEmailVerificationNotification();

// Check
$user->hasVerifiedEmail();
$user->markEmailAsVerified();
```

### Password Reset

```php
use Illuminate\Support\Facades\Password;
use Illuminate\Support\Facades\Hash;
use Illuminate\Auth\Events\PasswordReset;
use Illuminate\Support\Str;

// Send reset link
$status = Password::sendResetLink(['email' => $email]);

if ($status === Password::RESET_LINK_SENT) {
    return back()->with(['status' => __($status)]);
}

// Reset password
$status = Password::reset(
    $request->only('email', 'password', 'password_confirmation', 'token'),
    function (User $user, string $password) {
        $user->forceFill(['password' => Hash::make($password)])
             ->setRememberToken(Str::random(60));
        $user->save();
        event(new PasswordReset($user));
    }
);
```

---

## 18. Authorization (Gates & Policies)

### Gates

```php
// app/Providers/AppServiceProvider.php
use Illuminate\Support\Facades\Gate;

public function boot(): void
{
    // Simple gate
    Gate::define('update-post', function (User $user, Post $post) {
        return $user->id === $post->user_id;
    });

    // Gate with response
    Gate::define('admin-only', function (User $user) {
        return $user->is_admin
            ? Gate::allow()
            : Gate::deny('You must be an admin.');
    });

    // Before all gates
    Gate::before(function (User $user, string $ability) {
        if ($user->is_superadmin) return true;  // Allow everything
    });

    // After all gates
    Gate::after(function (User $user, string $ability, ?bool $result) {
        // Can modify result here
    });
}

// Using Gates
Gate::allows('update-post', $post);      // Bool
Gate::denies('update-post', $post);
Gate::check('update-post', $post);
Gate::any(['update-post', 'delete-post'], $post);  // Any of these
Gate::none(['update-post', 'delete-post'], $post); // None of these

// Authorize (throws AuthorizationException → 403)
Gate::authorize('update-post', $post);

// In controller
$this->authorize('update', $post);       // Uses policy
$this->authorize('update-post', $post);  // Uses gate

// In Blade
@can('update-post', $post) ... @endcan
@cannot('update-post', $post) ... @endcannot
```

### Policies

```bash
php artisan make:policy PostPolicy --model=Post
```

```php
// app/Policies/PostPolicy.php
namespace App\Policies;

use App\Models\Post;
use App\Models\User;
use Illuminate\Auth\Access\Response;

class PostPolicy
{
    // Called before all policy methods
    public function before(User $user, string $ability): bool|null
    {
        if ($user->is_admin) return true;   // Admin can do everything
        return null;                         // Continue to specific method
    }

    // viewAny is for listing
    public function viewAny(?User $user): bool
    {
        return true;  // Anyone can list (? = optional user for guest)
    }

    public function view(?User $user, Post $post): bool
    {
        if ($post->status === 'published') return true;
        return $user?->id === $post->user_id;
    }

    public function create(User $user): bool
    {
        return $user->hasVerifiedEmail();
    }

    public function update(User $user, Post $post): bool
    {
        return $user->id === $post->user_id;
    }

    public function delete(User $user, Post $post): Response
    {
        return $user->id === $post->user_id
            ? Response::allow()
            : Response::deny('You do not own this post.');
    }

    public function restore(User $user, Post $post): bool
    {
        return $user->id === $post->user_id;
    }

    public function forceDelete(User $user, Post $post): bool
    {
        return $user->is_admin;
    }

    // Custom action
    public function publish(User $user, Post $post): bool
    {
        return $user->id === $post->user_id && !$post->is_published;
    }
}


// Register policy (automatic discovery if naming convention followed)
// Or manually in AuthServiceProvider:
protected $policies = [
    Post::class => PostPolicy::class,
];


// Using policies in controllers
class PostController extends Controller
{
    public function update(UpdatePostRequest $request, Post $post): JsonResponse
    {
        $this->authorize('update', $post);   // 403 if fails
        $post->update($request->validated());
        return response()->json(new PostResource($post));
    }

    public function destroy(Post $post): Response
    {
        $this->authorize('delete', $post);
        $post->delete();
        return response()->noContent();
    }

    public function index(): PostCollection
    {
        $this->authorize('viewAny', Post::class);
        return new PostCollection(Post::paginate());
    }
}

// In Blade
@can('update', $post)
    <a href="{{ route('posts.edit', $post) }}">Edit</a>
@endcan

@can('create', App\Models\Post::class)
    <a href="{{ route('posts.create') }}">Create Post</a>
@endcan

// In middleware
Route::put('/posts/{post}', [PostController::class, 'update'])
    ->middleware('can:update,post');

// Checking in code
if (auth()->user()->can('update', $post)) { ... }
if (auth()->user()->cannot('delete', $post)) { ... }
```

---

## 19. File Storage

### Configuration

```php
// config/filesystems.php — Disk drivers
'disks' => [
    'local' => [
        'driver' => 'local',
        'root'   => storage_path('app'),
        'throw'  => false,
    ],
    'public' => [
        'driver'     => 'local',
        'root'       => storage_path('app/public'),
        'url'        => env('APP_URL') . '/storage',
        'visibility' => 'public',
    ],
    's3' => [
        'driver'   => 's3',
        'key'      => env('AWS_ACCESS_KEY_ID'),
        'secret'   => env('AWS_SECRET_ACCESS_KEY'),
        'region'   => env('AWS_DEFAULT_REGION'),
        'bucket'   => env('AWS_BUCKET'),
        'url'      => env('AWS_URL'),
        'endpoint' => env('AWS_ENDPOINT'),
        'use_path_style_endpoint' => env('AWS_USE_PATH_STYLE_ENDPOINT', false),
    ],
],
'default' => env('FILESYSTEM_DISK', 'local'),
```

```bash
# Create symbolic link: public/storage → storage/app/public
php artisan storage:link
```

### Storage Facade

```php
use Illuminate\Support\Facades\Storage;

// ─── STORING FILES ────────────────────────────────────
// Automatic unique name
$path = Storage::put('avatars', $request->file('avatar'));
$path = Storage::disk('s3')->put('avatars', $request->file('avatar'));

// Custom name
$path = Storage::putFileAs('avatars', $request->file('avatar'), 'photo.jpg');

// Put raw content
Storage::put('file.txt', 'Contents here');
Storage::prepend('file.txt', 'Content at start');
Storage::append('file.txt', 'Content at end');

// Visibility
Storage::put('file.jpg', $content, 'public');   // Public
Storage::put('file.jpg', $content, 'private');  // Private

// From request
$path = $request->file('photo')->store('photos');
$path = $request->file('photo')->store('photos', 's3');
$path = $request->file('photo')->storeAs('photos', 'custom.jpg');
$path = $request->file('photo')->storePublicly('photos');
$path = $request->file('photo')->storePubliclyAs('photos', 'custom.jpg');

// ─── RETRIEVING ───────────────────────────────────────
$contents = Storage::get('file.txt');
$exists   = Storage::exists('file.txt');
$missing  = Storage::missing('file.txt');
$size     = Storage::size('file.txt');
$modified = Storage::lastModified('file.txt');
$mime     = Storage::mimeType('file.txt');

// URL
$url = Storage::url('avatars/photo.jpg');         // /storage/avatars/photo.jpg
$url = Storage::temporaryUrl('file.jpg', now()->addMinutes(5));  // S3 signed URL

// Download
return Storage::download('file.txt');
return Storage::download('file.txt', 'Custom Name.txt', $headers);

// ─── LISTING ──────────────────────────────────────────
$files = Storage::files('avatars');             // Files in directory
$files = Storage::allFiles('avatars');          // Files in all subdirs
$dirs  = Storage::directories('posts');
$dirs  = Storage::allDirectories('posts');

// ─── MANAGING ─────────────────────────────────────────
Storage::copy('old/file.jpg', 'new/file.jpg');
Storage::move('old/file.jpg', 'new/file.jpg');
Storage::delete('file.txt');
Storage::delete(['file1.txt', 'file2.txt']);
Storage::makeDirectory('new-folder');
Storage::deleteDirectory('old-folder');

// ─── VISIBILITY ───────────────────────────────────────
Storage::setVisibility('file.jpg', 'public');
$visibility = Storage::visibility('file.jpg');  // 'public' or 'private'
```

---

## 20. Email & Notifications

### Creating Mailables

```bash
php artisan make:mail WelcomeMail --markdown=emails.welcome
php artisan make:mail InvoiceMail
```

```php
// app/Mail/WelcomeMail.php
namespace App\Mail;

use App\Models\User;
use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Mail\Mailables\Content;
use Illuminate\Mail\Mailables\Envelope;
use Illuminate\Mail\Mailables\Attachment;
use Illuminate\Queue\SerializesModels;

class WelcomeMail extends Mailable
{
    use Queueable, SerializesModels;

    public function __construct(
        public readonly User $user,
        private readonly string $activationToken,
    ) {}

    public function envelope(): Envelope
    {
        return new Envelope(
            subject: 'Welcome to ' . config('app.name') . '!',
            from: new Address('noreply@example.com', config('app.name')),
            replyTo: [
                new Address('support@example.com', 'Support Team'),
            ],
            cc: ['manager@example.com'],
            bcc: ['logging@example.com'],
            tags: ['welcome', 'onboarding'],
            metadata: ['user_id' => $this->user->id],
        );
    }

    public function content(): Content
    {
        return new Content(
            markdown: 'emails.welcome',      // Blade markdown template
            // view: 'emails.welcome',        // Or plain Blade view
            with: [
                'activationUrl' => route('verify.email', $this->activationToken),
            ],
        );
    }

    public function attachments(): array
    {
        return [
            Attachment::fromPath('/path/to/file.pdf')
                ->as('Report.pdf')
                ->withMime('application/pdf'),

            Attachment::fromStorage('reports/2024.pdf')
                ->as('Annual Report.pdf'),

            Attachment::fromData(
                fn() => $this->generatePDF(),
                'Invoice.pdf'
            )->withMime('application/pdf'),
        ];
    }
}
```

### Markdown Email Template

```blade
{{-- resources/views/emails/welcome.blade.php --}}
@component('mail::message')
# Welcome, {{ $user->name }}!

Thanks for signing up. Click the button below to verify your email.

@component('mail::button', ['url' => $activationUrl, 'color' => 'success'])
Verify Email Address
@endcomponent

If you didn't create an account, no action is required.

Thanks,
{{ config('app.name') }}
@endcomponent
```

### Sending Mail

```php
use Illuminate\Support\Facades\Mail;

// Send now
Mail::to($user)->send(new WelcomeMail($user, $token));
Mail::to('user@example.com', 'User Name')->send(new WelcomeMail($user, $token));
Mail::to($user)->cc($manager)->bcc($audit)->send(new WelcomeMail($user, $token));

// Queue mail
Mail::to($user)->queue(new WelcomeMail($user, $token));
Mail::to($user)->later(now()->addHours(1), new WelcomeMail($user, $token));

// Make mailable queueable by default
class WelcomeMail extends Mailable implements ShouldQueue { ... }

// Send to multiple
$users = User::all();
foreach ($users as $user) {
    Mail::to($user)->queue(new NewsletterMail($user));
}

// Raw mail
Mail::raw('Hello World!', function ($message) {
    $message->to('user@example.com')->subject('Test');
});
```

### Notifications

```bash
php artisan make:notification InvoicePaid
```

```php
// app/Notifications/InvoicePaid.php
namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Notifications\Notification;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Messages\BroadcastMessage;
use Illuminate\Notifications\Messages\SlackMessage;
use Illuminate\Contracts\Queue\ShouldQueue;

class InvoicePaid extends Notification implements ShouldQueue
{
    use Queueable;

    public function __construct(
        private readonly Invoice $invoice,
    ) {}

    // Which channels to use
    public function via(object $notifiable): array
    {
        return ['mail', 'database', 'broadcast'];
        // Available: mail, database, broadcast, nexmo, slack, vonage
    }

    // Email channel
    public function toMail(object $notifiable): MailMessage
    {
        return (new MailMessage)
            ->subject('Invoice #' . $this->invoice->id . ' Paid')
            ->greeting('Hello ' . $notifiable->name . '!')
            ->line('Your invoice has been paid.')
            ->line('Amount: $' . number_format($this->invoice->amount, 2))
            ->action('View Invoice', route('invoices.show', $this->invoice))
            ->line('Thank you for your payment!')
            ->salutation('Regards, ' . config('app.name'))
            ->success()      // or ->error(), ->warning()
            ->attach('/path/to/invoice.pdf', ['as' => 'Invoice.pdf']);
    }

    // Database channel (stores in notifications table)
    public function toDatabase(object $notifiable): array
    {
        return [
            'invoice_id' => $this->invoice->id,
            'amount'     => $this->invoice->amount,
            'message'    => 'Invoice #' . $this->invoice->id . ' has been paid.',
        ];
    }

    // Broadcast channel (real-time)
    public function toBroadcast(object $notifiable): BroadcastMessage
    {
        return new BroadcastMessage([
            'invoice_id' => $this->invoice->id,
            'message'    => 'Invoice paid!',
        ]);
    }
}


// Sending notifications
$user->notify(new InvoicePaid($invoice));

// Queue
$user->notify((new InvoicePaid($invoice))->delay(now()->addMinutes(5)));

// Via Notification facade
use Illuminate\Support\Facades\Notification;
Notification::send($users, new InvoicePaid($invoice));
Notification::route('mail', 'user@example.com')->notify(new InvoicePaid($invoice));


// Database notifications — requires migration
php artisan notifications:table
php artisan migrate

// Read notifications
$notifications = $user->notifications;              // All
$unread = $user->unreadNotifications;
$count  = $user->unreadNotifications()->count();
$user->notifications()->markAsRead();               // Mark all read
$user->unreadNotifications->markAsRead();
```

---

## 21. Queues & Jobs

### Setup

```bash
# Create jobs table
php artisan queue:table
php artisan migrate

# Or use Redis (no table needed)
QUEUE_CONNECTION=redis  # in .env
```

### Creating Jobs

```bash
php artisan make:job ProcessImage
php artisan make:job SendWelcomeEmail
```

```php
// app/Jobs/ProcessImage.php
namespace App\Jobs;

use App\Models\Post;
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;
use Illuminate\Bus\Batchable;

class ProcessImage implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels, Batchable;

    // Number of times to retry
    public int $tries = 3;

    // Retry delay in seconds
    public int $backoff = 60;           // Or array: [60, 120, 300]

    // Job timeout
    public int $timeout = 120;

    // Unique job (prevent duplicates)
    // implements ShouldBeUnique
    // public int $uniqueFor = 3600;   // Unique for 1 hour

    public function __construct(
        public readonly Post $post,
        public readonly string $operation = 'resize',
    ) {}

    public function handle(ImageService $imageService): void
    {
        // If part of a batch, check if cancelled
        if ($this->batch()?->cancelled()) return;

        $imageService->process($this->post, $this->operation);
        $this->post->update(['image_processed' => true]);
    }

    // How to handle failures
    public function failed(\Throwable $exception): void
    {
        // Notify admin, log, etc.
        Log::error('Image processing failed', [
            'post_id'   => $this->post->id,
            'exception' => $exception->getMessage(),
        ]);
        $this->post->author->notify(new JobFailedNotification($exception));
    }

    // Retry delay
    public function backoff(): array
    {
        return [30, 60, 120];   // Retry after 30s, 60s, then 120s
    }

    // When to give up retrying
    public function retryUntil(): \DateTime
    {
        return now()->addHours(2);
    }

    // Determine which queue to use
    public function queue(): string
    {
        return $this->post->is_featured ? 'high' : 'default';
    }
}
```

### Dispatching Jobs

```php
// Dispatch to queue
ProcessImage::dispatch($post);
ProcessImage::dispatch($post, 'thumbnail');

// Delay
ProcessImage::dispatch($post)->delay(now()->addMinutes(10));

// Specific queue
ProcessImage::dispatch($post)->onQueue('images');

// Specific connection
ProcessImage::dispatch($post)->onConnection('redis');

// Dispatch synchronously (bypass queue)
ProcessImage::dispatchSync($post);
ProcessImage::dispatchNow($post);

// Conditionally dispatch
ProcessImage::dispatchIf($post->has_image, $post);
ProcessImage::dispatchUnless($post->is_processed, $post);

// Chain jobs
ProcessImage::withChain([
    new OptimizeImage($post),
    new GenerateThumbnails($post),
    new NotifyAuthor($post),
])->dispatch($post);

// Job batches
use Illuminate\Bus\Batch;
use Illuminate\Support\Facades\Bus;

$batch = Bus::batch([
    new ProcessImage($post1),
    new ProcessImage($post2),
    new ProcessImage($post3),
])
->then(fn(Batch $batch) => Log::info('All jobs completed!'))
->catch(fn(Batch $batch, \Throwable $e) => Log::error('Batch failed', ['error' => $e->getMessage()]))
->finally(fn(Batch $batch) => Log::info('Batch finished'))
->onQueue('images')
->dispatch();

$batch->id;             // Batch ID
$batch->totalJobs;
$batch->processedJobs();
$batch->pendingJobs;
$batch->progress();    // 0-100
```

### Running Queue Workers

```bash
# Process jobs
php artisan queue:work
php artisan queue:work --queue=high,default    # Priority order
php artisan queue:work --connection=redis
php artisan queue:work --tries=3              # Max attempts
php artisan queue:work --timeout=90           # Timeout in seconds
php artisan queue:work --sleep=3              # Sleep if no jobs
php artisan queue:work --max-jobs=100         # Stop after N jobs
php artisan queue:work --max-time=3600        # Stop after N seconds
php artisan queue:work --daemon               # Don't restart on each job (prod)

# Process a single job
php artisan queue:work --once

# Listen (restarts on each job — good for dev)
php artisan queue:listen

# See failed jobs
php artisan queue:failed
php artisan queue:retry all          # Retry all failed
php artisan queue:retry <id>         # Retry specific
php artisan queue:forget <id>        # Delete failed job
php artisan queue:flush              # Delete all failed

# Horizon (advanced queue manager)
composer require laravel/horizon
php artisan horizon:install
php artisan horizon
```

---

## 22. Task Scheduling

### Defining Schedules

```php
// routes/console.php (Laravel 11+)
use Illuminate\Support\Facades\Schedule;

Schedule::command('emails:send')->daily();
Schedule::command('reports:generate')->weeklyOn(1, '8:00');

// app/Console/Kernel.php (Laravel 10 and below)
protected function schedule(Schedule $schedule): void
{
    // Commands
    $schedule->command('emails:send')
             ->daily()
             ->at('08:00')
             ->emailOutputTo('admin@example.com')
             ->sendOutputTo(storage_path('logs/schedule.log'))
             ->appendOutputTo(storage_path('logs/schedule.log'));

    $schedule->command('reports:generate', ['--format' => 'pdf'])
             ->weekly()
             ->mondays()
             ->at('09:00')
             ->timezone('America/New_York');

    // Closure
    $schedule->call(function () {
        DB::table('sessions')->where('last_activity', '<', now()->subDays(7))->delete();
    })->daily();

    // Job
    $schedule->job(new SendNewsletterJob)->dailyAt('06:00');
    $schedule->job(new Heartbeat, 'heartbeats', 'redis')->everyMinute();

    // Artisan closure command
    $schedule->call(function () {
        User::inactive()->delete();
    })->monthly();

    // Shell command
    $schedule->exec('node /home/forge/script.js')->daily();
}

// Frequency options
->everySecond()
->everyTwoSeconds()
->everyFiveSeconds()
->everyTenSeconds()
->everyThirtySeconds()
->everyMinute()
->everyTwoMinutes()
->everyFiveMinutes()
->everyTenMinutes()
->everyFifteenMinutes()
->everyThirtyMinutes()
->hourly()
->hourlyAt(17)                  // At :17 each hour
->everyOddHour()
->everyTwoHours()
->daily()
->dailyAt('13:00')
->twiceDaily(1, 13)
->weeklyOn(1, '8:00')           // Monday at 8am
->monthly()
->monthlyOn(4, '15:00')         // 4th of month at 3pm
->quarterly()
->yearly()
->yearlyOn(6, 1, '17:00')       // June 1st at 5pm
->cron('* * * * *')             // Custom cron

// Constraints
->weekdays()
->weekends()
->mondays()
->fridays()
->when(fn() => auth()->user()->isAdmin())
->skip(fn() => Report::todayExists())
->environments(['production', 'staging'])
->between('7:00', '22:00')
->unlessBetween('23:00', '6:00')

// Prevent overlapping
->withoutOverlapping()
->withoutOverlapping(10)         // Release lock after 10 minutes

// Run on one server only
->onOneServer()

// Background (don't wait for completion)
->runInBackground()
```

### Running the Scheduler

```bash
# Add to crontab (runs every minute)
* * * * * cd /path-to-project && php artisan schedule:run >> /dev/null 2>&1

# Test locally (no cron needed)
php artisan schedule:work         # Keeps running
php artisan schedule:run          # Run once
php artisan schedule:list         # Show all scheduled tasks
php artisan schedule:test         # Test specific task
```

---

## 23. Events & Listeners

### Defining Events

```bash
php artisan make:event PostPublished
php artisan make:listener SendPostPublishedNotification --event=PostPublished
php artisan event:generate  # Generate from EventServiceProvider definitions
```

```php
// app/Events/PostPublished.php
namespace App\Events;

use App\Models\Post;
use Illuminate\Broadcasting\Channel;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Broadcasting\PresenceChannel;
use Illuminate\Broadcasting\PrivateChannel;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class PostPublished implements ShouldBroadcast
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public function __construct(
        public readonly Post $post,
    ) {}

    // Broadcasting channel
    public function broadcastOn(): array
    {
        return [
            new Channel('posts'),
            new PrivateChannel('App.Models.User.' . $this->post->user_id),
        ];
    }

    public function broadcastAs(): string
    {
        return 'post.published';
    }

    public function broadcastWith(): array
    {
        return [
            'post_id' => $this->post->id,
            'title'   => $this->post->title,
        ];
    }
}
```

### Listeners

```php
// app/Listeners/SendPostPublishedNotification.php
namespace App\Listeners;

use App\Events\PostPublished;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;

class SendPostPublishedNotification implements ShouldQueue
{
    use InteractsWithQueue;

    public string $queue = 'notifications';
    public int $delay = 30;             // Delay in seconds

    public function handle(PostPublished $event): void
    {
        // Notify subscribers
        $event->post->author->followers->each(function ($follower) use ($event) {
            $follower->notify(new NewPostNotification($event->post));
        });
    }

    public function failed(PostPublished $event, \Throwable $exception): void
    {
        Log::error('Failed to send notification', ['post' => $event->post->id]);
    }

    // Conditionally handle
    public function shouldHandle(PostPublished $event): bool
    {
        return $event->post->is_published;
    }
}
```

### Registering Events

```php
// app/Providers/EventServiceProvider.php
protected $listen = [
    PostPublished::class => [
        SendPostPublishedNotification::class,
        UpdateSearchIndex::class,
        ClearPostCache::class,
    ],
    UserRegistered::class => [
        SendWelcomeEmail::class,
        CreateUserProfile::class,
    ],
];

// Or use automatic discovery
public function shouldDiscoverEvents(): bool
{
    return true;   // Auto-discovers events/listeners from the filesystem
}

// Dispatching events
event(new PostPublished($post));
PostPublished::dispatch($post);
Event::dispatch(new PostPublished($post));

// Fire and forget (no response needed)
event(new PostViewed($post));

// Conditionally
PostPublished::dispatchIf($post->is_published, $post);
```

---

## 24. Service Container & Providers

### Service Container (IoC Container)

```php
use Illuminate\Support\Facades\App;

// ─── BINDING ──────────────────────────────────────────
// Bind interface to implementation
app()->bind(PaymentInterface::class, StripePayment::class);

// Singleton (one instance for the entire application)
app()->singleton(DatabaseConnection::class, function ($app) {
    return new DatabaseConnection(config('database.default'));
});

// Instance (bind existing instance)
app()->instance('config', $configObject);

// Scoped (one instance per request)
app()->scoped(RequestContext::class, function ($app) {
    return new RequestContext(request());
});

// ─── RESOLVING ────────────────────────────────────────
$payment = app(PaymentInterface::class);
$payment = app()->make(PaymentInterface::class);
$payment = resolve(PaymentInterface::class);

// With parameters
$payment = app()->makeWith(StripePayment::class, ['secret' => env('STRIPE_SECRET')]);

// ─── AUTO-WIRING ──────────────────────────────────────
// Laravel automatically resolves type-hinted dependencies
class PostController extends Controller
{
    public function __construct(
        private readonly PostRepository $posts,     // Auto-injected!
        private readonly CacheService $cache,       // Auto-injected!
    ) {}
}

// ─── CONTEXTUAL BINDING ───────────────────────────────
app()->when(PhotoController::class)
     ->needs(Storage::class)
     ->give(S3Storage::class);

app()->when(VideoController::class)
     ->needs(Storage::class)
     ->give(LocalStorage::class);

// ─── TAGGING ──────────────────────────────────────────
app()->bind(CsvReport::class, fn() => new CsvReport);
app()->bind(PdfReport::class, fn() => new PdfReport);
app()->tag([CsvReport::class, PdfReport::class], 'reports');

$reports = app()->tagged('reports');
```

### Service Providers

```bash
php artisan make:provider PaymentServiceProvider
```

```php
// app/Providers/PaymentServiceProvider.php
namespace App\Providers;

use Illuminate\Support\ServiceProvider;

class PaymentServiceProvider extends ServiceProvider
{
    // Deferred loading (only load when needed)
    public bool $defer = true;

    // Register bindings in the container
    public function register(): void
    {
        $this->app->singleton(PaymentGateway::class, function ($app) {
            return new StripeGateway(
                $app['config']['services.stripe.key'],
                $app['config']['services.stripe.secret'],
            );
        });

        $this->app->bind(PaymentInterface::class, function ($app) {
            return $app->make(PaymentGateway::class);
        });

        // Merge config
        $this->mergeConfigFrom(__DIR__ . '/../../config/payment.php', 'payment');
    }

    // Bootstrap services (runs after all providers registered)
    public function boot(): void
    {
        // Publish config
        $this->publishes([
            __DIR__ . '/../../config/payment.php' => config_path('payment.php'),
        ], 'config');

        // Publish views
        $this->publishes([
            __DIR__ . '/../../resources/views' => resource_path('views/vendor/payment'),
        ], 'views');

        // Load views
        $this->loadViewsFrom(__DIR__ . '/../../resources/views', 'payment');

        // Load migrations
        $this->loadMigrationsFrom(__DIR__ . '/../../database/migrations');

        // Load routes
        $this->loadRoutesFrom(__DIR__ . '/../../routes/payment.php');

        // Register event listeners
        Event::listen(PaymentCompleted::class, SendReceipt::class);

        // Register observers
        Invoice::observe(InvoiceObserver::class);

        // Register macros
        Collection::macro('toUpper', function () {
            return $this->map(fn($item) => strtoupper($item));
        });
    }

    // If deferred, declare what bindings are provided
    public function provides(): array
    {
        return [PaymentGateway::class, PaymentInterface::class];
    }
}

// Register in bootstrap/providers.php (Laravel 11) or config/app.php (Laravel 10)
```

### Repository Pattern

```php
// app/Repositories/Contracts/PostRepositoryInterface.php
interface PostRepositoryInterface
{
    public function all(array $filters = []);
    public function find(int $id): Post;
    public function create(array $data): Post;
    public function update(int $id, array $data): Post;
    public function delete(int $id): bool;
}

// app/Repositories/PostRepository.php
class PostRepository implements PostRepositoryInterface
{
    public function __construct(private readonly Post $model) {}

    public function all(array $filters = [])
    {
        return $this->model->query()
            ->when($filters['status'] ?? null, fn($q, $v) => $q->where('status', $v))
            ->when($filters['search'] ?? null, fn($q, $v) => $q->where('title', 'like', "%$v%"))
            ->with(['author', 'tags'])
            ->paginate($filters['per_page'] ?? 15);
    }

    public function find(int $id): Post
    {
        return $this->model->with(['author', 'comments', 'tags'])->findOrFail($id);
    }

    public function create(array $data): Post
    {
        return $this->model->create($data);
    }

    public function update(int $id, array $data): Post
    {
        $post = $this->find($id);
        $post->update($data);
        return $post->fresh();
    }

    public function delete(int $id): bool
    {
        return $this->find($id)->delete();
    }
}

// Bind in AppServiceProvider
$this->app->bind(PostRepositoryInterface::class, PostRepository::class);

// Use in controller
class PostController extends Controller
{
    public function __construct(
        private readonly PostRepositoryInterface $posts,
    ) {}
}
```

---

## 25. Facades

Facades provide a static interface to classes available in the service container.

```php
// Common Facades
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\Config;
use Illuminate\Support\Facades\Cookie;
use Illuminate\Support\Facades\Crypt;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Event;
use Illuminate\Support\Facades\File;
use Illuminate\Support\Facades\Gate;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Facades\Http;
use Illuminate\Support\Facades\Lang;
use Illuminate\Support\Facades\Log;
use Illuminate\Support\Facades\Mail;
use Illuminate\Support\Facades\Notification;
use Illuminate\Support\Facades\Queue;
use Illuminate\Support\Facades\Redirect;
use Illuminate\Support\Facades\Redis;
use Illuminate\Support\Facades\Request;
use Illuminate\Support\Facades\Response;
use Illuminate\Support\Facades\Route;
use Illuminate\Support\Facades\Schema;
use Illuminate\Support\Facades\Session;
use Illuminate\Support\Facades\Storage;
use Illuminate\Support\Facades\URL;
use Illuminate\Support\Facades\Validator;
use Illuminate\Support\Facades\View;

// Hash
Hash::make('password');
Hash::check('plain', $hashed);
Hash::needsRehash($hashed);

// Crypt
Crypt::encryptString('secret');
Crypt::decryptString($encrypted);

// URL
URL::to('/posts/1');
URL::route('posts.show', 1);
URL::current();
URL::full();
URL::previous();
URL::signedRoute('verify', ['id' => 1]);           // Signed URL
URL::temporarySignedRoute('preview', now()->addHours(1), ['id' => 1]);

// HTTP Client
Http::get('https://api.example.com/users');
Http::post('https://api.example.com/users', ['name' => 'John']);
Http::withToken($token)->get('https://api.example.com/me');
Http::withHeaders(['Accept' => 'application/json'])->get('...');
Http::timeout(30)->retry(3, 100)->get('...');
Http::async()->get('...');
$response = Http::get('...');
$response->json();
$response->status();
$response->successful();  // 2xx
$response->failed();      // 4xx or 5xx

// Custom Facade
// 1. Create service class
class PaymentService { public function charge($amount) { ... } }

// 2. Register in container
app()->bind('payment', fn() => new PaymentService());

// 3. Create facade
class Payment extends \Illuminate\Support\Facades\Facade
{
    protected static function getFacadeAccessor(): string { return 'payment'; }
}

// 4. Usage
Payment::charge(100);
```

---

## 26. Caching

### Cache Usage

```php
use Illuminate\Support\Facades\Cache;

// ─── STORING ──────────────────────────────────────────
Cache::put('key', 'value', 3600);              // Seconds
Cache::put('key', 'value', now()->addHours(1)); // Carbon
Cache::put('key', 'value');                    // Forever
Cache::forever('key', 'value');                // Explicit forever

Cache::add('key', 'value', 3600);             // Only if doesn't exist (returns bool)
Cache::set('key', 'value', 3600);             // PSR-16 compliant

// ─── RETRIEVING ───────────────────────────────────────
$value = Cache::get('key');
$value = Cache::get('key', 'default');
$value = Cache::get('key', fn() => 'computed-default');

// Get or store pattern
$posts = Cache::remember('all-posts', 3600, function () {
    return Post::with('author')->get();
});

// Remember forever
$config = Cache::rememberForever('site-config', fn() => Config::all());

// Get and forget
$value = Cache::pull('key');

// ─── CHECKING ─────────────────────────────────────────
Cache::has('key');
Cache::missing('key');

// ─── DELETING ─────────────────────────────────────────
Cache::forget('key');
Cache::delete('key');
Cache::flush();                                 // Clear everything!

// ─── INCREMENT / DECREMENT ────────────────────────────
Cache::increment('visitors');
Cache::increment('visitors', 5);
Cache::decrement('likes');

// ─── MULTIPLE ─────────────────────────────────────────
Cache::putMany(['key1' => 'val1', 'key2' => 'val2'], 3600);
$values = Cache::many(['key1', 'key2']);
Cache::deleteMany(['key1', 'key2']);

// ─── TAGS (Redis/Memcached only) ──────────────────────
Cache::tags(['posts', 'category:1'])->put('post:1', $post, 3600);
Cache::tags(['posts', 'category:1'])->get('post:1');
Cache::tags('posts')->flush();                  // Clear all 'posts' tagged

// ─── LOCK (atomic) ────────────────────────────────────
$lock = Cache::lock('process-payment-' . $orderId, 10);

if ($lock->get()) {
    try {
        processPayment($orderId);
    } finally {
        $lock->release();
    }
}

// Block until lock available
$lock->block(5, function () use ($orderId) {
    processPayment($orderId);
});

// ─── DIFFERENT STORES ─────────────────────────────────
Cache::store('redis')->put('key', 'value', 3600);
Cache::store('file')->get('key');
Cache::store('array')->put('key', 'test');  // Great for testing
```

### Cache in Routes

```php
// Route caching
Route::middleware('cache.headers:public;max_age=2628000;etag')
    ->group(function () {
        Route::get('/posts/{post}', [PostController::class, 'show']);
    });

// Response cache
return response()->json($data)
    ->header('Cache-Control', 'public, max-age=3600');
```

---

## 27. Sessions

```php
use Illuminate\Support\Facades\Session;

// ─── STORING ──────────────────────────────────────────
session(['key' => 'value']);
session()->put('key', 'value');
Session::put('key', 'value');
Session::put(['key1' => 'val1', 'key2' => 'val2']);
Session::push('my.array', 'new_item');  // Append to array

// ─── RETRIEVING ───────────────────────────────────────
$value = session('key');
$value = session('key', 'default');
$value = Session::get('key');
$value = Session::get('key', 'default');
$all   = Session::all();
$has   = Session::has('key');     // Exists & not null
$exists= Session::exists('key');  // Even if null

// Pull (get and forget)
$value = Session::pull('key');

// ─── FLASH DATA (one request only) ───────────────────
Session::flash('success', 'Post created successfully!');
Session::flash('error', 'Something went wrong.');
Session::reflash();                  // Keep flash data one more request
Session::keep(['success', 'error']); // Keep specific keys

// In Blade
@if (session('success'))
    <div class="alert alert-success">{{ session('success') }}</div>
@endif

// ─── DELETING ─────────────────────────────────────────
Session::forget('key');
Session::forget(['key1', 'key2']);
Session::flush();                    // Clear all session data

// ─── REGENERATE ───────────────────────────────────────
Session::regenerate();               // New session ID (prevent fixation)
Session::invalidate();              // Flush + regenerate

// ─── IN CONTROLLERS ───────────────────────────────────
public function login(Request $request): RedirectResponse
{
    // Login logic...
    $request->session()->put('user_id', $user->id);
    $request->session()->regenerate();
    return redirect()->intended('/dashboard');
}

public function logout(Request $request): RedirectResponse
{
    $request->session()->invalidate();
    $request->session()->regenerateToken();
    return redirect('/');
}
```

---

## 28. Logging

### Using the Logger

```php
use Illuminate\Support\Facades\Log;

Log::emergency('System is unusable!');
Log::alert('Action must be taken immediately.');
Log::critical('Critical condition occurred.');
Log::error('An error occurred: {message}', ['message' => $e->getMessage()]);
Log::warning('Deprecated feature used.');
Log::notice('Significant event occurred.');
Log::info('User logged in.', ['user_id' => $user->id]);
Log::debug('Debug details.', ['data' => $data]);

// Context data
Log::info('User action', [
    'user_id'   => auth()->id(),
    'action'    => 'post.created',
    'post_id'   => $post->id,
    'ip'        => request()->ip(),
    'timestamp' => now()->toISOString(),
]);

// Different channels
Log::channel('slack')->critical('Server down!');
Log::channel('daily')->info('Daily log entry.');
Log::stack(['single', 'slack'])->error('Error on both channels.');

// Contextual logging
Log::withContext(['request_id' => $requestId])->info('Processing...');
```

### Log Channels (config/logging.php)

```php
'channels' => [
    'stack' => [
        'driver'   => 'stack',
        'channels' => ['single', 'slack'],
    ],
    'single' => [
        'driver' => 'single',
        'path'   => storage_path('logs/laravel.log'),
        'level'  => 'debug',
    ],
    'daily' => [
        'driver' => 'daily',
        'path'   => storage_path('logs/laravel.log'),
        'level'  => 'debug',
        'days'   => 14,                    // Keep 14 days of logs
    ],
    'slack' => [
        'driver'   => 'slack',
        'url'      => env('LOG_SLACK_WEBHOOK_URL'),
        'username' => 'Laravel Log',
        'emoji'    => ':boom:',
        'level'    => 'critical',
    ],
    'papertrail' => [
        'driver'       => 'monolog',
        'level'        => 'debug',
        'handler'      => SyslogUdpHandler::class,
        'handler_with' => [
            'host' => env('PAPERTRAIL_URL'),
            'port' => env('PAPERTRAIL_PORT'),
        ],
    ],
],
```

---

## 29. Testing

### Setup

```php
// tests/TestCase.php
namespace Tests;

use Illuminate\Foundation\Testing\TestCase as BaseTestCase;
use Illuminate\Foundation\Testing\RefreshDatabase;

abstract class TestCase extends BaseTestCase
{
    use RefreshDatabase;  // Rollback DB after each test
    // use DatabaseMigrations;  // Migrate fresh before each test
    // use DatabaseTransactions;  // Wrap in transaction, rollback after
}
```

### Feature Tests

```php
// tests/Feature/PostTest.php
namespace Tests\Feature;

use App\Models\Post;
use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Http\UploadedFile;
use Illuminate\Support\Facades\Storage;
use Tests\TestCase;

class PostTest extends TestCase
{
    use RefreshDatabase;

    private User $user;
    private User $admin;

    protected function setUp(): void
    {
        parent::setUp();
        $this->user  = User::factory()->create();
        $this->admin = User::factory()->admin()->create();
    }

    // GET /api/posts
    public function test_guest_can_list_published_posts(): void
    {
        Post::factory()->count(3)->published()->create();
        Post::factory()->count(2)->draft()->create();

        $response = $this->getJson('/api/posts');

        $response->assertOk()
                 ->assertJsonCount(3, 'data')
                 ->assertJsonStructure([
                     'data' => [['id', 'title', 'status', 'author']],
                     'meta' => ['total', 'per_page', 'current_page'],
                 ]);
    }

    // POST /api/posts
    public function test_authenticated_user_can_create_post(): void
    {
        $data = [
            'title'       => 'My First Post',
            'content'     => str_repeat('Some content. ', 20),
            'category_id' => 1,
            'status'      => 'draft',
        ];

        $response = $this->actingAs($this->user)
                         ->postJson('/api/posts', $data);

        $response->assertCreated()
                 ->assertJsonPath('data.title', 'My First Post')
                 ->assertJsonPath('data.author.id', $this->user->id);

        $this->assertDatabaseHas('posts', [
            'title'   => 'My First Post',
            'user_id' => $this->user->id,
        ]);
    }

    public function test_guest_cannot_create_post(): void
    {
        $response = $this->postJson('/api/posts', ['title' => 'Test']);
        $response->assertUnauthorized();
    }

    public function test_post_creation_requires_title(): void
    {
        $response = $this->actingAs($this->user)
                         ->postJson('/api/posts', ['content' => 'Some content here...']);

        $response->assertUnprocessable()
                 ->assertJsonValidationErrors(['title']);
    }

    // File upload
    public function test_user_can_upload_post_cover(): void
    {
        Storage::fake('public');

        $post  = Post::factory()->forUser($this->user)->create();
        $file  = UploadedFile::fake()->image('cover.jpg', 800, 600);

        $response = $this->actingAs($this->user)
                         ->postJson("/api/posts/{$post->id}/cover", [
                             'cover' => $file,
                         ]);

        $response->assertOk();
        Storage::disk('public')->assertExists("covers/{$file->hashName()}");
    }

    // Authorization
    public function test_user_cannot_update_others_post(): void
    {
        $post = Post::factory()->create();  // Belongs to another user

        $response = $this->actingAs($this->user)
                         ->putJson("/api/posts/{$post->id}", ['title' => 'Hacked']);

        $response->assertForbidden();
    }

    // Event dispatching
    public function test_event_dispatched_when_post_published(): void
    {
        Event::fake();

        $post = Post::factory()->forUser($this->user)->draft()->create();

        $this->actingAs($this->user)
             ->postJson("/api/posts/{$post->id}/publish")
             ->assertOk();

        Event::assertDispatched(PostPublished::class, function ($event) use ($post) {
            return $event->post->id === $post->id;
        });
    }

    // Mail
    public function test_welcome_email_sent_on_registration(): void
    {
        Mail::fake();

        $this->postJson('/api/register', [
            'name'                  => 'John Doe',
            'email'                 => 'john@example.com',
            'password'              => 'password123',
            'password_confirmation' => 'password123',
        ])->assertCreated();

        Mail::assertSent(WelcomeMail::class, function ($mail) {
            return $mail->hasTo('john@example.com');
        });
    }

    // Queue
    public function test_image_processing_job_dispatched(): void
    {
        Queue::fake();

        $post = Post::factory()->create();

        $this->actingAs($this->user)
             ->postJson("/api/posts/{$post->id}/process");

        Queue::assertPushed(ProcessImage::class, function ($job) use ($post) {
            return $job->post->id === $post->id;
        });
    }

    // Notification
    public function test_user_notified_when_post_liked(): void
    {
        Notification::fake();

        $post = Post::factory()->forUser($this->user)->published()->create();

        $liker = User::factory()->create();
        $this->actingAs($liker)->postJson("/api/posts/{$post->id}/like")->assertOk();

        Notification::assertSentTo($this->user, PostLikedNotification::class);
    }
}
```

### Unit Tests

```php
// tests/Unit/PostTest.php
namespace Tests\Unit;

use App\Models\Post;
use Tests\TestCase;

class PostTest extends TestCase
{
    public function test_slug_generated_from_title(): void
    {
        $post = Post::factory()->make(['title' => 'Hello World']);
        $this->assertEquals('hello-world', $post->slug);
    }

    public function test_read_time_calculated_correctly(): void
    {
        $post = Post::factory()->make([
            'content' => str_repeat('word ', 600),  // 600 words
        ]);
        $this->assertEquals(3, $post->read_time);  // 600/200 = 3 min
    }

    public function test_summary_truncated_to_150_chars(): void
    {
        $post = Post::factory()->make([
            'content' => str_repeat('a', 300),
        ]);
        $this->assertEquals(150, strlen($post->summary));
    }
}
```

### Useful Testing Helpers

```php
// Acting as a user
$this->actingAs($user);
$this->actingAs($user, 'api');      // Specific guard

// Fake services
Event::fake();
Mail::fake();
Queue::fake();
Notification::fake();
Storage::fake('s3');
Http::fake([...]);
Bus::fake();

// Assertions
$response->assertOk();              // 200
$response->assertCreated();         // 201
$response->assertNoContent();       // 204
$response->assertNotFound();        // 404
$response->assertForbidden();       // 403
$response->assertUnauthorized();    // 401
$response->assertUnprocessable();   // 422
$response->assertRedirect('/home');
$response->assertSessionHas('key', 'value');
$response->assertSessionHasErrors(['email']);
$response->assertViewIs('posts.index');
$response->assertSee('Hello World');
$response->assertDontSee('Error');
$response->assertJson(['key' => 'value']);
$response->assertJsonPath('data.title', 'Hello World');
$response->assertJsonCount(5, 'data');
$response->assertJsonStructure(['data' => ['*' => ['id', 'title']]]);
$response->assertJsonMissing(['password']);
$response->assertHeader('Content-Type', 'application/json');
$response->assertCookie('name');

// Database
$this->assertDatabaseHas('posts', ['title' => 'Hello', 'user_id' => 1]);
$this->assertDatabaseMissing('posts', ['title' => 'Deleted Post']);
$this->assertDatabaseCount('posts', 10);
$this->assertSoftDeleted($post);
$this->assertNotSoftDeleted($post);
$this->assertModelExists($post);
$this->assertModelMissing($deletedPost);
```

---

## 30. API Development (with Sanctum)

### Setup Sanctum

```bash
composer require laravel/sanctum
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
php artisan migrate
```

```php
// app/Models/User.php
use Laravel\Sanctum\HasApiTokens;

class User extends Authenticatable
{
    use HasApiTokens, HasFactory, Notifiable;
}

// config/sanctum.php
'stateful' => explode(',', env('SANCTUM_STATEFUL_DOMAINS', sprintf(
    '%s%s',
    'localhost,localhost:3000,127.0.0.1,127.0.0.1:8000,::1',
    Str::startsWith(env('APP_URL', ''), 'https://') ? ','.parse_url(env('APP_URL', ''), PHP_URL_HOST) : ''
))),

// routes/api.php
Route::middleware('auth:sanctum')->group(function () {
    Route::apiResource('posts', PostController::class);
    Route::get('/user', fn(Request $r) => $r->user());
    Route::delete('/logout', [AuthController::class, 'logout']);
});
```

### Auth API Controller

```php
// app/Http/Controllers/Api/AuthController.php
class AuthController extends Controller
{
    public function register(RegisterRequest $request): JsonResponse
    {
        $user = User::create([
            'name'     => $request->name,
            'email'    => $request->email,
            'password' => Hash::make($request->password),
        ]);

        $token = $user->createToken('auth_token', ['*'])->plainTextToken;

        return response()->json([
            'user'         => new UserResource($user),
            'access_token' => $token,
            'token_type'   => 'Bearer',
        ], 201);
    }

    public function login(LoginRequest $request): JsonResponse
    {
        if (! Auth::attempt($request->only('email', 'password'))) {
            throw ValidationException::withMessages([
                'email' => ['The provided credentials are incorrect.'],
            ]);
        }

        $user  = Auth::user();
        $token = $user->createToken('auth_token', ['*'])->plainTextToken;

        return response()->json([
            'user'         => new UserResource($user),
            'access_token' => $token,
            'token_type'   => 'Bearer',
        ]);
    }

    public function logout(Request $request): JsonResponse
    {
        // Revoke current token
        $request->user()->currentAccessToken()->delete();

        // Revoke all tokens
        // $request->user()->tokens()->delete();

        return response()->json(['message' => 'Logged out successfully.']);
    }

    public function user(Request $request): UserResource
    {
        return new UserResource($request->user());
    }
}


// Token abilities (scopes)
$token = $user->createToken('mobile-app', ['read:posts', 'write:posts']);

// Check ability
if ($request->user()->tokenCan('write:posts')) { ... }

// Middleware
Route::middleware(['auth:sanctum', 'abilities:read:posts'])->get('/posts', ...);
Route::middleware(['auth:sanctum', 'ability:read:posts,write:posts'])->post('/posts', ...);

// Token expiration
'expiration' => 525600,  // In minutes (365 days). null = never.

// Revoke specific token
$user->tokens()->where('id', $tokenId)->delete();
// Or
PersonalAccessToken::find($tokenId)->delete();
```

---

## 31. Broadcasting (WebSockets)

### Setup

```bash
# Pusher
composer require pusher/pusher-php-server
BROADCAST_DRIVER=pusher

# Laravel Reverb (self-hosted)
composer require laravel/reverb
php artisan reverb:install
BROADCAST_DRIVER=reverb
php artisan reverb:start
```

### Events

```php
// app/Events/MessageSent.php
class MessageSent implements ShouldBroadcast
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public function __construct(
        public readonly User $user,
        public readonly Message $message,
    ) {}

    public function broadcastOn(): array
    {
        return [
            new Channel('chat'),                          // Public
            new PrivateChannel('chat.' . $this->message->room_id),  // Private (auth required)
            new PresenceChannel('chat.' . $this->message->room_id), // Presence (who's online)
        ];
    }

    public function broadcastAs(): string
    {
        return 'message.sent';     // Event name on JS side
    }

    public function broadcastWith(): array
    {
        return [
            'id'         => $this->message->id,
            'content'    => $this->message->content,
            'user'       => ['id' => $this->user->id, 'name' => $this->user->name],
            'created_at' => $this->message->created_at->toISOString(),
        ];
    }

    // Only broadcast if condition met
    public function broadcastWhen(): bool
    {
        return $this->message->is_approved;
    }
}

// Dispatch event
broadcast(new MessageSent($user, $message));
broadcast(new MessageSent($user, $message))->toOthers(); // Exclude sender
```

### Channel Authorization

```php
// routes/channels.php
use App\Models\User;

Broadcast::channel('App.Models.User.{id}', function (User $user, int $id) {
    return (int) $user->id === (int) $id;
});

// Private channel
Broadcast::channel('chat.{roomId}', function (User $user, int $roomId) {
    return $user->canJoinRoom($roomId);
});

// Presence channel (return user data)
Broadcast::channel('presence.chat.{roomId}', function (User $user, int $roomId) {
    if ($user->canJoinRoom($roomId)) {
        return ['id' => $user->id, 'name' => $user->name, 'avatar' => $user->avatar_url];
    }
    return false;
});
```

### JavaScript Client

```javascript
// resources/js/echo.js
import Echo from 'laravel-echo';
import Pusher from 'pusher-js';

window.Pusher = Pusher;
window.Echo = new Echo({
    broadcaster: 'pusher',
    key: import.meta.env.VITE_PUSHER_APP_KEY,
    cluster: import.meta.env.VITE_PUSHER_APP_CLUSTER,
    forceTLS: true,
});

// Subscribe to public channel
Echo.channel('chat')
    .listen('MessageSent', (e) => {
        console.log(e.message, e.user);
    });

// Subscribe to private channel
Echo.private(`chat.${roomId}`)
    .listen('MessageSent', (e) => {
        console.log('Private:', e);
    });

// Presence channel
Echo.join(`presence.chat.${roomId}`)
    .here((users) => console.log('Currently here:', users))
    .joining((user) => console.log('Joined:', user.name))
    .leaving((user) => console.log('Left:', user.name))
    .listen('MessageSent', (e) => console.log(e));

// Listen to notifications
Echo.private(`App.Models.User.${userId}`)
    .notification((notification) => {
        console.log(notification.type);
    });
```

---

## 32. Artisan Console Commands

### Creating Commands

```bash
php artisan make:command SendDailyReport
```

```php
// app/Console/Commands/SendDailyReport.php
namespace App\Console\Commands;

use Illuminate\Console\Command;
use App\Services\ReportService;

class SendDailyReport extends Command
{
    protected $signature = 'reports:send
        {type : The type of report (daily|weekly|monthly)}
        {--email=admin@example.com : Email to send the report to}
        {--dry-run : Run without actually sending}
        {users?* : Specific user IDs}';

    protected $description = 'Generate and send a report to the specified email.';

    public function __construct(
        private readonly ReportService $reportService,
    ) {
        parent::__construct();
    }

    public function handle(): int
    {
        $type  = $this->argument('type');
        $email = $this->option('email');
        $dryRun= $this->option('dry-run');
        $users = $this->argument('users');

        // Validate
        if (! in_array($type, ['daily', 'weekly', 'monthly'])) {
            $this->error("Invalid type: $type");
            return Command::FAILURE;
        }

        // Interact with user
        if ($this->confirm("Send $type report to $email?", true)) {
            $email = $this->ask('Confirm email address:', $email);
        }

        // Progress bar
        $users = User::all();
        $bar   = $this->output->createProgressBar($users->count());
        $bar->start();

        $users->each(function ($user) use (&$bar) {
            $this->reportService->process($user);
            $bar->advance();
        });

        $bar->finish();
        $this->newLine();

        // Output
        $this->info("✓ Report generated successfully!");
        $this->line("Sent to: $email");
        $this->warn("This may take a moment to arrive.");
        $this->error("This is an error message.");
        $this->comment("This is a comment.");

        // Table output
        $this->table(
            ['ID', 'Name', 'Email', 'Posts'],
            User::with('posts')->get()->map(fn($u) => [
                $u->id, $u->name, $u->email, $u->posts->count()
            ])
        );

        // Choice
        $framework = $this->choice('Which framework?', ['Laravel', 'Symfony', 'None'], 0);

        return Command::SUCCESS;   // Or Command::FAILURE
    }
}

// Register (automatic in Laravel 11+ via autodiscovery)
// In Kernel.php (Laravel 10):
protected $commands = [
    Commands\SendDailyReport::class,
];
```

### Closure Commands

```php
// routes/console.php
Artisan::command('inspire', function () {
    $this->comment(Inspiring::quote());
})->purpose('Display an inspiring quote');

Artisan::command('users:count', function () {
    $count = \App\Models\User::count();
    $this->info("Total users: $count");
})->purpose('Show total user count');
```

### Calling Commands from Code

```php
// In controller or service
Artisan::call('reports:send', [
    'type'   => 'daily',
    '--email'=> 'admin@example.com',
]);

// Capture output
$output = Artisan::output();

// Queue a command
Artisan::queue('emails:send', ['--all' => true])->onQueue('commands');

// Call from another command
$this->call('migrate', ['--force' => true]);
$this->callSilently('cache:clear');
```

---

## 33. Deployment

### Environment Setup

```bash
# 1. Clone repo
git clone https://github.com/user/project.git
cd project

# 2. Install dependencies
composer install --no-dev --optimize-autoloader

# 3. Set up .env
cp .env.example .env
php artisan key:generate

# 4. Edit .env for production settings

# 5. Run migrations
php artisan migrate --force

# 6. Seed if needed
php artisan db:seed --force

# 7. Cache everything
php artisan config:cache
php artisan route:cache
php artisan view:cache
php artisan event:cache
php artisan optimize

# 8. Link storage
php artisan storage:link

# 9. Set permissions
chmod -R 775 storage bootstrap/cache
chown -R www-data:www-data storage bootstrap/cache
```

### Nginx Configuration

```nginx
server {
    listen 80;
    server_name example.com www.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name example.com www.example.com;
    root /var/www/project/public;

    ssl_certificate /etc/ssl/certs/example.com.crt;
    ssl_certificate_key /etc/ssl/private/example.com.key;

    index index.php index.html;
    client_max_body_size 20M;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php8.2-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

### Supervisor (Queue Workers)

```ini
; /etc/supervisor/conf.d/laravel-worker.conf
[program:laravel-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /var/www/project/artisan queue:work redis --sleep=3 --tries=3 --max-time=3600
autostart=true
autorestart=true
stopasgroup=true
killasgroup=true
user=www-data
numprocs=4
redirect_stderr=true
stdout_logfile=/var/www/project/storage/logs/worker.log
stopwaitsecs=3600

[program:laravel-scheduler]
command=/bin/bash -c "while [ true ]; do (php /var/www/project/artisan schedule:run --verbose --no-interaction &); sleep 60; done"
user=www-data
autostart=true
autorestart=true
stdout_logfile=/var/www/project/storage/logs/scheduler.log
```

```bash
sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl start all
sudo supervisorctl status
```

### Dockerfile

```dockerfile
FROM php:8.2-fpm-alpine

WORKDIR /var/www/html

RUN apk add --no-cache \
    bash git curl libpng-dev libjpeg-turbo-dev freetype-dev \
    postgresql-dev zip libzip-dev nodejs npm

RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
 && docker-php-ext-install pdo pdo_pgsql pdo_mysql gd bcmath zip pcntl

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

COPY composer.json composer.lock ./
RUN composer install --no-dev --optimize-autoloader --no-scripts

COPY package.json package-lock.json ./
RUN npm ci

COPY . .
RUN npm run build \
 && composer dump-autoload --optimize \
 && php artisan config:cache \
 && php artisan route:cache \
 && php artisan view:cache

RUN chown -R www-data:www-data storage bootstrap/cache

EXPOSE 9000
CMD ["php-fpm"]
```

### docker-compose.yml

```yaml
version: '3.9'

services:
  app:
    build: .
    volumes:
      - .:/var/www/html
      - ./storage:/var/www/html/storage
    env_file: .env
    depends_on:
      - db
      - redis

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - .:/var/www/html
      - ./docker/nginx/nginx.conf:/etc/nginx/conf.d/default.conf
    depends_on:
      - app

  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: ${DB_DATABASE}
      POSTGRES_USER: ${DB_USERNAME}
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    command: redis-server --appendonly yes
    volumes:
      - redis_data:/data

  queue:
    build: .
    command: php artisan queue:work --sleep=3 --tries=3 --max-time=3600
    env_file: .env
    depends_on:
      - db
      - redis

  scheduler:
    build: .
    command: php artisan schedule:work
    env_file: .env
    depends_on:
      - db
      - redis

volumes:
  postgres_data:
  redis_data:
```

---

## 34. Security Best Practices

```php
// 1. Always use environment variables
$key = env('STRIPE_SECRET');         // ✅
$key = 'sk_live_hardcoded';          // ❌ Never!

// 2. Validate everything
$data = $request->validate([...]);   // ✅ Always validate
$data = $request->all();             // ❌ Never use raw input

// 3. Use mass assignment protection
protected $fillable = ['name', 'email']; // ✅ Whitelist
// protected $guarded = [];           // ❌ Allows everything

// 4. Eloquent prevents SQL injection
User::where('email', $email)->get(); // ✅ Parameterized
DB::select("SELECT * WHERE email='$email'"); // ❌ Never!

// 5. CSRF protection (automatic for web routes)
// @csrf in all forms

// 6. XSS protection
{{ $userInput }}    // ✅ Escaped
{!! $userInput !!}  // ❌ Unescaped — only for trusted HTML

// 7. Authorization
$this->authorize('update', $post);   // ✅ Always authorize

// 8. Rate limiting
Route::post('/login', LoginController::class)->middleware('throttle:5,1');

// 9. Signed URLs for sensitive actions
URL::signedRoute('unsubscribe', ['user' => $user->id]);

// 10. Secure cookies
SESSION_COOKIE_SECURE=true
SESSION_COOKIE_HTTPONLY=true
SESSION_COOKIE_SAMESITE=lax

// 11. Hash passwords
Hash::make($password);               // ✅
$password;                           // ❌ Never store plain text

// 12. Encrypt sensitive data
Crypt::encryptString($ssn);
Crypt::decryptString($encrypted);
// Or use 'encrypted' cast on model

// 13. Content Security Policy (add CSP headers)
// Use spatie/laravel-csp package

// 14. Security headers in AppServiceProvider or middleware
return $next($request)->withHeaders([
    'X-Frame-Options'         => 'DENY',
    'X-Content-Type-Options'  => 'nosniff',
    'Referrer-Policy'         => 'strict-origin-when-cross-origin',
    'Strict-Transport-Security'=> 'max-age=31536000; includeSubDomains',
]);

// 15. Prevent user enumeration in password reset
// Laravel's built-in does this by design
```

---

## 35. Common Patterns & Tips

### Service Layer Pattern

```php
// app/Services/PostService.php
class PostService
{
    public function __construct(
        private readonly PostRepository $repository,
        private readonly TagService $tagService,
        private readonly ImageService $imageService,
    ) {}

    public function create(array $data, User $author): Post
    {
        return DB::transaction(function () use ($data, $author) {
            $post = $this->repository->create([
                ...$data,
                'user_id' => $author->id,
                'slug'    => Str::slug($data['title']),
            ]);

            if (isset($data['tags'])) {
                $this->tagService->sync($post, $data['tags']);
            }

            if (isset($data['cover'])) {
                $this->imageService->processAndStore($post, $data['cover']);
            }

            event(new PostCreated($post));
            return $post;
        });
    }
}
```

### Action Classes (Single Responsibility)

```php
// app/Actions/PublishPost.php
class PublishPost
{
    public function execute(Post $post): Post
    {
        throw_unless(
            $post->status === 'draft',
            new InvalidArgumentException('Post must be a draft to publish.')
        );

        $post->update([
            'status'       => 'published',
            'published_at' => now(),
        ]);

        event(new PostPublished($post));
        Cache::forget("post:{$post->id}");

        return $post->fresh();
    }
}

// Usage in controller
class PublishPostController extends Controller
{
    public function __invoke(Post $post, PublishPost $action): JsonResponse
    {
        $this->authorize('update', $post);
        return response()->json(new PostResource($action->execute($post)));
    }
}
```

### Data Transfer Objects (DTOs)

```php
// app/DTOs/CreatePostData.php
class CreatePostData
{
    public function __construct(
        public readonly string $title,
        public readonly string $content,
        public readonly string $status,
        public readonly ?int $categoryId,
        public readonly array $tags = [],
    ) {}

    public static function fromRequest(StorePostRequest $request): self
    {
        return new self(
            title:      $request->input('title'),
            content:    $request->input('content'),
            status:     $request->input('status', 'draft'),
            categoryId: $request->input('category_id'),
            tags:       $request->input('tags', []),
        );
    }
}
```

### Useful Helpers

```php
// String helpers (Str facade or global str())
use Illuminate\Support\Str;

Str::slug('Hello World');            // 'hello-world'
Str::snake('helloWorld');            // 'hello_world'
Str::camel('hello_world');           // 'helloWorld'
Str::studly('hello_world');          // 'HelloWorld'
Str::title('hello world');           // 'Hello World'
Str::upper('hello');                 // 'HELLO'
Str::lower('HELLO');                 // 'hello'
Str::limit('Long text here', 20);    // 'Long text here...'
Str::words('Long text', 3);          // 'Long text...'
Str::contains('hello world', 'world');
Str::startsWith('hello', 'he');
Str::endsWith('hello', 'lo');
Str::random(40);                     // Random string
Str::uuid();                         // UUID
Str::ulid();                         // ULID
Str::plural('cat');                  // 'cats'
Str::singular('cats');               // 'cat'
Str::wordCount('hello world');       // 2
Str::of('hello')->upper()->slug();   // Fluent string

// Number helpers
Number::format(1234567.89);          // '1,234,567.89'
Number::currency(42.5, 'USD');       // '$42.50'
Number::percentage(75);              // '75%'
Number::fileSize(1024);              // '1 KB'
Number::abbreviate(1000000);         // '1M'
Number::spell(5);                    // 'five'

// Array helpers (Arr facade)
use Illuminate\Support\Arr;

Arr::get($array, 'user.address.city', 'default');
Arr::set($array, 'user.name', 'John');
Arr::has($array, 'user.email');
Arr::forget($array, 'user.password');
Arr::only($array, ['name', 'email']);
Arr::except($array, ['password', 'token']);
Arr::pluck($array, 'name');
Arr::first($array, fn($v) => $v > 5);
Arr::flatten($array);
Arr::sort($array);
Arr::sortByMany($array, ['name', '-age']);
Arr::random($array, 3);
Arr::shuffle($array);
Arr::wrap('string');                 // ['string']
Arr::wrap(['array']);                // ['array']
Arr::wrap(null);                     // []

// Carbon date helpers
use Illuminate\Support\Carbon;

Carbon::now();
Carbon::today();
Carbon::tomorrow();
Carbon::yesterday();
Carbon::parse('2024-01-15');
Carbon::createFromFormat('d/m/Y', '15/01/2024');
now()->addDays(7);
now()->subMonths(1);
now()->startOfWeek();
now()->endOfMonth();
now()->isFuture();
now()->isPast();
now()->isToday();
now()->diffForHumans();              // '3 days ago'
now()->format('Y-m-d H:i:s');
now()->toISOString();
```

### Useful Third-Party Packages

```bash
# Debugging
composer require barryvdh/laravel-debugbar --dev     # Debug toolbar
composer require barryvdh/laravel-ide-helper --dev   # IDE autocomplete

# Admin panel
composer require filament/filament

# API
composer require spatie/laravel-query-builder         # Filter/sort/include API
composer require spatie/laravel-fractal               # Transformers

# Auth
composer require laravel/jetstream                    # Full auth with teams
composer require laravel/socialite                    # OAuth (Google, GitHub, etc.)

# Media
composer require spatie/laravel-media-library         # File management
composer require intervention/image                   # Image manipulation

# Utilities
composer require spatie/laravel-permission            # Roles & permissions
composer require spatie/laravel-activity-log         # Activity logging
composer require spatie/laravel-backup               # Database backups
composer require spatie/laravel-sluggable            # Auto-slugs
composer require spatie/laravel-translatable         # Multi-language models
composer require spatie/laravel-tags                 # Tagging
composer require spatie/laravel-settings             # App settings
composer require spatie/laravel-model-states         # State machines

# Search
composer require laravel/scout                        # Full-text search driver
composer require typesense/typesense-php              # Typesense
composer require meilisearch/meilisearch-php          # MeiliSearch

# Monitoring
composer require laravel/telescope                    # Debug & monitoring
composer require laravel/horizon                      # Queue monitoring
composer require sentry/sentry-laravel               # Error tracking
```

---

## 36 Localization

# 🌍 Laravel Localization — Complete Guide
### Covers: Blade · React + Inertia.js · Laravel 12 Starter Kit

> Written for developers with full-stack experience who are new to localization.
> This guide covers **both worlds** — classic Blade AND modern React (Inertia.js) with the official Laravel 12 React Starter Kit.

---

## 📚 Table of Contents

**PART 1 — The Core Concept (Read This First)**
1. [Why React Localization is Different from Blade](#1-why-react-localization-is-different-from-blade)
2. [Two Strategies for React + Inertia](#2-two-strategies-for-react--inertia)

**PART 2 — Laravel 12 Starter Kit Setup**
3. [Installing Laravel 12 with the React Starter Kit](#3-installing-laravel-12-with-the-react-starter-kit)
4. [What the Starter Kit Gives You](#4-what-the-starter-kit-gives-you)
5. [Registering Middleware in Laravel 12 (bootstrap/app.php)](#5-registering-middleware-in-laravel-12-bootstrapappphp)

**PART 3 — The Best Approach: `laravel-react-i18n`**
6. [Install the `laravel-react-i18n` Package](#6-install-the-laravel-react-i18n-package)
7. [Set Up Language Files (JSON format)](#7-set-up-language-files-json-format)
8. [Wrap Your App with the Provider (app.tsx)](#8-wrap-your-app-with-the-provider-apptsx)
9. [Share the Locale from Laravel to React (HandleInertiaRequests)](#9-share-the-locale-from-laravel-to-react-handleinertiarequests)
10. [Using Translations in React Components](#10-using-translations-in-react-components)
11. [Switching Language (React + Route)](#11-switching-language-react--route)
12. [SetLocale Middleware for Laravel 12](#12-setlocale-middleware-for-laravel-12)

**PART 4 — Manual Approach (No Package)**
13. [Pass Translations via Inertia Shared Props (Manual)](#13-pass-translations-via-inertia-shared-props-manual)
14. [Create a `useTrans` Hook in React](#14-create-a-usetrans-hook-in-react)

**PART 5 — Validation, Flash & Backend Strings**
15. [Translating Validation Messages](#15-translating-validation-messages)
16. [Translating Flash Messages](#16-translating-flash-messages)

**PART 6 — Reference**
17. [Commands Cheat Sheet](#17-commands-cheat-sheet)
18. [Full Project File Structure](#18-full-project-file-structure)
19. [Troubleshooting](#19-troubleshooting)
20. [Which Approach Should You Use?](#20-which-approach-should-you-use)

---

## 1. Why React Localization is Different from Blade

This is the **most important thing to understand** before anything else.

### In Blade (simple)
```php
// PHP runs on the server, renders HTML, sends it to the browser
<h1>{{ __('messages.welcome') }}</h1>
// ✅ Easy — PHP knows the locale, translates directly
```

### In React + Inertia (different)
```jsx
// React runs in the BROWSER — PHP is already done by the time JSX renders
<h1>{__('messages.welcome')}</h1>
// ❌ This won't work — __() is a PHP function, it doesn't exist in JavaScript!
```

**The core problem:** Your translations live in PHP files. Your React components run in the browser. You need a **bridge** to get translations from PHP → JavaScript.

There are two solutions to this problem:

---

## 2. Two Strategies for React + Inertia

| Strategy | How it works | Best for |
|----------|-------------|----------|
| **`laravel-react-i18n` package** ✅ Recommended | Compiles your PHP `lang/` files to JSON at build time. Loads them in React via a Provider. | Most projects — clean, simple, mirrors Laravel's `__()` syntax |
| **Manual via Inertia Shared Props** | PHP serializes translations to JSON, passes them via `HandleInertiaRequests`, React reads from `usePage().props` | Full control, no extra package, small apps |

We'll cover **both** — but start with the recommended approach.

---

## 3. Installing Laravel 12 with the React Starter Kit

### Fresh install (recommended)

```bash
# Install Laravel installer if you don't have it
composer global require laravel/installer

# Create a new Laravel 12 project with React starter kit
laravel new my-app --using=react

# OR use composer directly
composer create-project laravel/laravel my-app
cd my-app
php artisan install:api  # Not needed — React kit is different
```

### The official way for the React Starter Kit

```bash
# This is the single command that sets up Laravel 12 + React + Inertia + TypeScript + Tailwind + shadcn/ui
laravel new my-app

# When prompted, choose:
# Which starter kit? → React
# Which testing framework? → Pest (recommended) or PHPUnit
```

### Install dependencies after cloning

```bash
composer install
npm install
cp .env.example .env
php artisan key:generate
php artisan migrate
npm run dev
```

---

## 4. What the Starter Kit Gives You

The Laravel 12 React Starter Kit includes:

```
my-app/
├── resources/
│   └── js/
│       ├── app.tsx                    ← Main entry point (Inertia setup)
│       ├── pages/                     ← Your React page components
│       │   ├── auth/                  ← Login, register, etc.
│       │   └── dashboard.tsx
│       ├── layouts/                   ← App layouts
│       ├── components/                ← Reusable UI components (shadcn/ui)
│       └── types/                     ← TypeScript types
├── app/
│   └── Http/
│       └── Middleware/
│           └── HandleInertiaRequests.php  ← KEY FILE for sharing data to React
├── bootstrap/
│   └── app.php                        ← Middleware registration (Laravel 12 way)
├── routes/
│   └── web.php
└── lang/                              ← Created after php artisan lang:publish
```

### Key file to know: `HandleInertiaRequests.php`

This is the **bridge** between Laravel and React. Whatever you put in the `share()` method becomes available in every React component via `usePage().props`.

```php
// app/Http/Middleware/HandleInertiaRequests.php
public function share(Request $request): array
{
    return [
        ...parent::share($request),
        'auth' => ['user' => $request->user()],
        // ↑ This is how auth.user is available in every React component
        // We will add locale and translations here too
    ];
}
```

---

## 5. Registering Middleware in Laravel 12 (`bootstrap/app.php`)

> ⚠️ **Laravel 12 is different from Laravel 10.** There is NO `Kernel.php` file anymore.
> Everything goes in `bootstrap/app.php`.

```php
// bootstrap/app.php
<?php

use Illuminate\Foundation\Application;
use Illuminate\Foundation\Configuration\Exceptions;
use Illuminate\Foundation\Configuration\Middleware;

return Application::configure(basePath: dirname(__DIR__))
    ->withRouting(
        web: __DIR__.'/../routes/web.php',
        commands: __DIR__.'/../routes/console.php',
        health: '/up',
    )
    ->withMiddleware(function (Middleware $middleware) {
        $middleware->web(append: [
            \App\Http\Middleware\HandleInertiaRequests::class,  // Already there in starter kit
            \App\Http\Middleware\SetLocale::class,              // ← Add this for localization
        ]);
    })
    ->withExceptions(function (Exceptions $exceptions) {
        //
    })->create();
```

> 💡 **Note:** `HandleInertiaRequests` is already registered in the starter kit. You only need to add `SetLocale`.

---

## 6. Install the `laravel-react-i18n` Package

This is the cleanest, most production-ready approach. It mirrors Laravel's own `__()` and `trans_choice()` API exactly.

### Step 1 — Install the PHP side (for compiling lang files)

```bash
# Install the Vite plugin that compiles your lang/ PHP files to JSON
npm install laravel-react-i18n
```

### Step 2 — Install pre-built translations (optional but recommended)

```bash
composer require laravel-lang/lang --dev
composer require laravel-lang/publisher --dev

# Publish the lang folder
php artisan lang:publish

# Add your languages
php artisan lang:add ur fr ar
```

### Step 3 — Configure `vite.config.ts`

Open your `vite.config.ts` and add the `i18n` plugin:

```typescript
// vite.config.ts
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
import react from '@vitejs/plugin-react';
import i18n from 'laravel-react-i18n/vite';  // ← Add this import

export default defineConfig({
    plugins: [
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.tsx'],
            refresh: true,
        }),
        react(),
        i18n(),  // ← Add this plugin — compiles lang/ files to JSON at build time
    ],
});
```

---

## 7. Set Up Language Files (JSON format)

For `laravel-react-i18n`, your language files must be **JSON** (not PHP arrays) at the root of `lang/`.

### Create `lang/en.json`

```json
{
    "Welcome": "Welcome",
    "Dashboard": "Dashboard",
    "Login": "Login",
    "Logout": "Logout",
    "Register": "Register",
    "Profile": "Profile",
    "Settings": "Settings",
    "Save": "Save",
    "Cancel": "Cancel",
    "Hello, :name!": "Hello, :name!",
    "You have :count messages.": "You have :count messages.",
    "No items|One item|:count items": "No items|One item|:count items"
}
```

### Create `lang/ur.json`

```json
{
    "Welcome": "خوش آمدید",
    "Dashboard": "ڈیش بورڈ",
    "Login": "لاگ ان",
    "Logout": "لاگ آؤٹ",
    "Register": "رجسٹر کریں",
    "Profile": "پروفائل",
    "Settings": "ترتیبات",
    "Save": "محفوظ کریں",
    "Cancel": "منسوخ کریں",
    "Hello, :name!": "ہیلو، :name!",
    "You have :count messages.": "آپ کے :count پیغامات ہیں۔",
    "No items|One item|:count items": "کوئی آئٹم نہیں|ایک آئٹم|:count آئٹمز"
}
```

### Create `lang/fr.json`

```json
{
    "Welcome": "Bienvenue",
    "Dashboard": "Tableau de bord",
    "Login": "Connexion",
    "Logout": "Déconnexion",
    "Register": "S'inscrire",
    "Save": "Sauvegarder",
    "Cancel": "Annuler",
    "Hello, :name!": "Bonjour, :name!"
}
```

> 💡 **Note:** You can also use PHP array files (`lang/en/messages.php`) alongside JSON files.
> The package handles both. JSON at the root level (`lang/en.json`) is the most common approach.

---

## 8. Wrap Your App with the Provider (`app.tsx`)

Open `resources/js/app.tsx` — this is the Inertia entry point that the starter kit gives you.

```typescript
// resources/js/app.tsx
import './bootstrap';
import '../css/app.css';

import { createRoot } from 'react-dom/client';
import { createInertiaApp } from '@inertiajs/react';
import { resolvePageComponent } from 'laravel-vite-plugin/inertia-helpers';
import { LaravelReactI18nProvider } from 'laravel-react-i18n';  // ← Import

const appName = import.meta.env.VITE_APP_NAME || 'Laravel';

createInertiaApp({
    title: (title) => `${title} - ${appName}`,
    resolve: (name) =>
        resolvePageComponent(
            `./pages/${name}.tsx`,
            import.meta.glob('./pages/**/*.tsx'),
        ),
    setup({ el, App, props }) {
        const root = createRoot(el);

        // Read the initial locale from Inertia shared props
        // We will share this from HandleInertiaRequests (next step)
        const initialLocale = (props.initialPage.props as any).locale || 'en';

        root.render(
            // ← Wrap App with the provider
            <LaravelReactI18nProvider
                locale={initialLocale}
                fallbackLocale="en"
                files={import.meta.glob('/lang/*.json', { eager: true })}
            >
                <App {...props} />
            </LaravelReactI18nProvider>,
        );
    },
    progress: {
        color: '#4B5563',
    },
});
```

---

## 9. Share the Locale from Laravel to React (`HandleInertiaRequests`)

React needs to know the current locale on every page load. We share it through `HandleInertiaRequests.php`.

```php
<?php
// app/Http/Middleware/HandleInertiaRequests.php

namespace App\Http\Middleware;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\App;
use Inertia\Middleware;

class HandleInertiaRequests extends Middleware
{
    protected $rootView = 'app';

    public function version(Request $request): ?string
    {
        return parent::version($request);
    }

    public function share(Request $request): array
    {
        return [
            ...parent::share($request),
            'auth' => [
                'user' => $request->user(),
            ],
            // ← ADD THESE TWO:
            'locale' => App::getLocale(),               // Current locale (e.g. 'en', 'ur')
            'available_locales' => ['en', 'ur', 'fr'],  // All supported locales (for your switcher UI)
        ];
    }
}
```

Now `locale` and `available_locales` are available in **every React component** via `usePage().props`.

---

## 10. Using Translations in React Components

Now you can use the `useLaravelReactI18n` hook anywhere in your React components.

### Basic translation

```tsx
// resources/js/pages/dashboard.tsx
import { useLaravelReactI18n } from 'laravel-react-i18n';

export default function Dashboard() {
    const { t } = useLaravelReactI18n();

    return (
        <div>
            <h1>{t('Welcome')}</h1>
            <p>{t('Dashboard')}</p>
        </div>
    );
}
```

### With variable replacement

```tsx
import { useLaravelReactI18n } from 'laravel-react-i18n';

export default function Profile({ user }: { user: { name: string } }) {
    const { t } = useLaravelReactI18n();

    return (
        <div>
            {/* Outputs: "Hello, Ahmad!" */}
            <h2>{t('Hello, :name!', { name: user.name })}</h2>

            {/* Outputs: "You have 5 messages." */}
            <p>{t('You have :count messages.', { count: 5 })}</p>
        </div>
    );
}
```

### Pluralization with `tChoice()`

```tsx
import { useLaravelReactI18n } from 'laravel-react-i18n';

export default function ItemList({ items }: { items: any[] }) {
    const { tChoice } = useLaravelReactI18n();

    return (
        <div>
            {/* Outputs: "No items", "One item", or "5 items" */}
            <p>{tChoice('No items|One item|:count items', items.length, { count: items.length })}</p>
        </div>
    );
}
```

### Checking and switching locale at runtime

```tsx
import { useLaravelReactI18n } from 'laravel-react-i18n';

export default function SomeComponent() {
    const { t, currentLocale, setLocale, getLocales, loading } = useLaravelReactI18n();

    return (
        <div>
            <p>Current language: {currentLocale()}</p>

            {/* Show loading state while switching */}
            {loading && <span>Loading translations...</span>}

            {/* Client-side only switch (does NOT persist on refresh) */}
            <button onClick={() => setLocale('ur')}>اردو</button>
            <button onClick={() => setLocale('en')}>English</button>
        </div>
    );
}
```

> ⚠️ `setLocale()` only switches locale **on the client side** during the current session.
> To persist the locale across page reloads, use the route-based switcher below.

---

## 11. Switching Language (React + Route)

For persistent locale switching (saved in session, survives page refresh):

### Step 1 — Add the route in `routes/web.php`

```php
// routes/web.php
use Illuminate\Support\Facades\Route;
use Illuminate\Support\Facades\Session;
use Illuminate\Support\Facades\App;

Route::get('/lang/{locale}', function (string $locale) {
    $supported = ['en', 'ur', 'fr', 'ar'];

    if (in_array($locale, $supported)) {
        Session::put('locale', $locale);
    }

    return redirect()->back();
})->name('lang.switch');
```

### Step 2 — Create a `LanguageSwitcher` React component

```tsx
// resources/js/components/LanguageSwitcher.tsx
import { usePage, router } from '@inertiajs/react';
import { useLaravelReactI18n } from 'laravel-react-i18n';

const flags: Record<string, string> = {
    en: '🇬🇧',
    ur: '🇵🇰',
    fr: '🇫🇷',
    ar: '🇸🇦',
};

const labels: Record<string, string> = {
    en: 'English',
    ur: 'اردو',
    fr: 'Français',
    ar: 'العربية',
};

export default function LanguageSwitcher() {
    const { locale, available_locales } = usePage().props as {
        locale: string;
        available_locales: string[];
    };

    const { setLocale } = useLaravelReactI18n();

    function switchLocale(newLocale: string) {
        // 1. Update the React provider immediately (instant UI response)
        setLocale(newLocale);

        // 2. Also persist to session via the Laravel route (survives refresh)
        router.get(route('lang.switch', newLocale), {}, {
            preserveState: true,
            preserveScroll: true,
        });
    }

    return (
        <div className="flex gap-2">
            {available_locales.map((loc) => (
                <button
                    key={loc}
                    onClick={() => switchLocale(loc)}
                    className={`px-3 py-1 rounded text-sm ${
                        locale === loc
                            ? 'bg-blue-600 text-white'
                            : 'bg-gray-100 hover:bg-gray-200'
                    }`}
                >
                    {flags[loc]} {labels[loc]}
                </button>
            ))}
        </div>
    );
}
```

### Step 3 — Use it in your layout

```tsx
// resources/js/layouts/app-layout.tsx (or wherever your nav is)
import LanguageSwitcher from '@/components/LanguageSwitcher';

export default function AppLayout({ children }: { children: React.ReactNode }) {
    return (
        <div>
            <nav className="flex items-center justify-between p-4">
                <span>My App</span>
                <LanguageSwitcher />  {/* ← Add here */}
            </nav>
            <main>{children}</main>
        </div>
    );
}
```

---

## 12. SetLocale Middleware for Laravel 12

```bash
php artisan make:middleware SetLocale
```

```php
<?php
// app/Http/Middleware/SetLocale.php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\App;
use Illuminate\Support\Facades\Session;
use Symfony\Component\HttpFoundation\Response;

class SetLocale
{
    public function handle(Request $request, Closure $next): Response
    {
        $supported = ['en', 'ur', 'fr', 'ar'];
        $locale = Session::get('locale', config('app.locale'));

        // Safety check
        if (!in_array($locale, $supported)) {
            $locale = config('app.locale');
        }

        App::setLocale($locale);

        return $next($request);
    }
}
```

### Register in `bootstrap/app.php` (Laravel 12 way — no Kernel.php!)

```php
// bootstrap/app.php
->withMiddleware(function (Middleware $middleware) {
    $middleware->web(append: [
        \App\Http\Middleware\HandleInertiaRequests::class,
        \App\Http\Middleware\SetLocale::class,  // ← Add this
    ]);
})
```

---

## 13. Pass Translations via Inertia Shared Props (Manual)

> Use this if you don't want to install any npm package. It's more work but gives full control.

### Step 1 — Share translations in `HandleInertiaRequests.php`

```php
// app/Http/Middleware/HandleInertiaRequests.php

use Illuminate\Support\Facades\App;
use Illuminate\Support\Facades\File;

public function share(Request $request): array
{
    return [
        ...parent::share($request),
        'auth' => ['user' => $request->user()],
        'locale' => App::getLocale(),
        'translations' => $this->loadTranslations(),
    ];
}

private function loadTranslations(): array
{
    $locale = App::getLocale();
    $path = lang_path("{$locale}");

    if (!File::isDirectory($path)) {
        return [];
    }

    $translations = [];

    foreach (File::files($path) as $file) {
        $key = $file->getFilenameWithoutExtension(); // e.g. 'messages'
        $translations[$key] = require $file->getPathname();
    }

    return $translations;
}
```

### Step 2 — Create a `useTrans` hook in React

```tsx
// resources/js/hooks/useTrans.ts
import { usePage } from '@inertiajs/react';

export function useTrans() {
    const { translations } = usePage().props as { translations: Record<string, any> };

    function t(key: string, replacements: Record<string, string | number> = {}): string {
        // key format: 'file.key' e.g. 'messages.welcome'
        const parts = key.split('.');
        const file = parts[0];
        const rest = parts.slice(1).join('.');

        let value: any = translations?.[file];

        // Support nested keys: 'messages.nav.home'
        for (const part of rest.split('.')) {
            if (value && typeof value === 'object') {
                value = value[part];
            } else {
                return key; // key not found — return key as fallback
            }
        }

        if (typeof value !== 'string') return key;

        // Replace :placeholder with actual values
        return Object.entries(replacements).reduce((str, [k, v]) => {
            return str.replace(new RegExp(`:${k}`, 'g'), String(v));
        }, value);
    }

    return { t };
}
```

### Step 3 — Use in React components

```tsx
// resources/js/pages/dashboard.tsx
import { useTrans } from '@/hooks/useTrans';

export default function Dashboard() {
    const { t } = useTrans();

    return (
        <div>
            <h1>{t('messages.welcome')}</h1>
            <p>{t('messages.hello', { name: 'Ahmad' })}</p>
        </div>
    );
}
```

---

## 14. Create a `useTrans` Hook in React

> This section is already covered above in Step 13 — here's the TypeScript-safe extended version.

```tsx
// resources/js/hooks/useTrans.ts (extended version with pluralization)
import { usePage } from '@inertiajs/react';

export function useTrans() {
    const { translations } = usePage().props as { translations: Record<string, any> };

    function t(key: string, replacements: Record<string, string | number> = {}): string {
        const [file, ...rest] = key.split('.');
        let value: any = translations?.[file];

        for (const part of rest) {
            value = value?.[part];
        }

        if (typeof value !== 'string') return key;

        return Object.entries(replacements).reduce(
            (str, [k, v]) => str.replace(new RegExp(`:${k}`, 'gi'), String(v)),
            value
        );
    }

    function tChoice(key: string, count: number, replacements: Record<string, string | number> = {}): string {
        const raw = t(key, replacements);
        const parts = raw.split('|');

        let selected: string;

        if (parts.length === 2) {
            selected = count === 1 ? parts[0] : parts[1];
        } else if (parts.length >= 3) {
            if (count === 0) selected = parts[0];
            else if (count === 1) selected = parts[1];
            else selected = parts[2];
        } else {
            selected = raw;
        }

        return selected.replace(/:count/g, String(count));
    }

    return { t, tChoice };
}
```

---

## 15. Translating Validation Messages

Validation errors come from PHP and arrive in React via Inertia's `errors` prop automatically. You only need to translate the PHP side.

### Install pre-built validation translations

```bash
php artisan lang:add ur
```

This creates `lang/ur/validation.php` with all Laravel validation messages in Urdu.

### Custom validation messages (in your form request)

```php
// app/Http/Requests/StorePostRequest.php
public function messages(): array
{
    return [
        'title.required' => __('validation.custom.title.required'),
        'body.min'        => __('validation.custom.body.min'),
    ];
}
```

### Displaying errors in React (already handled by Inertia)

```tsx
// The `errors` prop is automatically injected by Inertia
import { useForm } from '@inertiajs/react';

export default function CreatePost() {
    const { data, setData, post, errors } = useForm({
        title: '',
        body: '',
    });

    return (
        <form onSubmit={(e) => { e.preventDefault(); post('/posts'); }}>
            <input
                value={data.title}
                onChange={(e) => setData('title', e.target.value)}
            />
            {/* errors.title comes from Laravel validation — already translated on PHP side */}
            {errors.title && <p className="text-red-500">{errors.title}</p>}
        </form>
    );
}
```

---

## 16. Translating Flash Messages

Flash messages are set in PHP and passed to React via shared props.

### Step 1 — Set flash message in controller

```php
// app/Http/Controllers/PostController.php
public function store(Request $request)
{
    // ... save post
    return redirect()->back()->with('success', __('messages.post_created'));
}
```

### Step 2 — Share flash in `HandleInertiaRequests.php`

```php
public function share(Request $request): array
{
    return [
        ...parent::share($request),
        'auth'   => ['user' => $request->user()],
        'locale' => App::getLocale(),
        'flash'  => [
            'success' => fn () => $request->session()->get('success'),
            'error'   => fn () => $request->session()->get('error'),
        ],
    ];
}
```

### Step 3 — Display in React

```tsx
// resources/js/components/FlashMessage.tsx
import { usePage } from '@inertiajs/react';

export default function FlashMessage() {
    const { flash } = usePage().props as {
        flash: { success?: string; error?: string };
    };

    if (!flash.success && !flash.error) return null;

    return (
        <div>
            {flash.success && (
                <div className="bg-green-100 text-green-800 p-3 rounded">
                    {flash.success}
                </div>
            )}
            {flash.error && (
                <div className="bg-red-100 text-red-800 p-3 rounded">
                    {flash.error}
                </div>
            )}
        </div>
    );
}
```

---

## 17. Commands Cheat Sheet

```bash
# ── LARAVEL 12 SETUP ───────────────────────────────────────────────────────

# Create new Laravel 12 app with React starter kit
laravel new my-app --using=react

# Publish lang/ folder
php artisan lang:publish

# Install pre-built translations
composer require laravel-lang/lang laravel-lang/publisher --dev
php artisan lang:add ur fr ar
php artisan lang:list        # See all available languages
php artisan lang:update      # Sync after package updates
php artisan lang:remove fr   # Remove a language


# ── REACT i18n PACKAGE ─────────────────────────────────────────────────────

npm install laravel-react-i18n


# ── MIDDLEWARE ─────────────────────────────────────────────────────────────

php artisan make:middleware SetLocale
# Then register in bootstrap/app.php (NOT Kernel.php in Laravel 12!)


# ── CACHE & CONFIG ─────────────────────────────────────────────────────────

php artisan config:clear
php artisan cache:clear
php artisan view:clear
php artisan route:clear


# ── DEV SERVER ─────────────────────────────────────────────────────────────

php artisan serve          # Start PHP server
npm run dev                # Start Vite (keep running alongside artisan serve)
# OR
composer run dev           # Starts both together (in Laravel 12 starter kit!)


# ── BUILD FOR PRODUCTION ───────────────────────────────────────────────────

npm run build              # Builds React + compiles lang/ files to JSON
```

---

## 18. Full Project File Structure

```
my-app/
│
├── bootstrap/
│   └── app.php                          ← Register SetLocale middleware HERE (Laravel 12)
│
├── lang/
│   ├── en.json                          ← English (for laravel-react-i18n)
│   ├── ur.json                          ← Urdu
│   ├── fr.json                          ← French
│   ├── en/
│   │   ├── messages.php                 ← PHP-style (for __() in Blade/controllers)
│   │   ├── validation.php
│   │   └── auth.php
│   └── ur/
│       ├── messages.php
│       ├── validation.php
│       └── auth.php
│
├── app/
│   └── Http/
│       └── Middleware/
│           ├── HandleInertiaRequests.php ← Share locale + translations with React
│           └── SetLocale.php             ← Apply locale from session on every request
│
├── routes/
│   └── web.php                           ← /lang/{locale} switch route
│
└── resources/
    └── js/
        ├── app.tsx                        ← Wrap with LaravelReactI18nProvider
        ├── components/
        │   ├── LanguageSwitcher.tsx       ← Language switcher UI component
        │   └── FlashMessage.tsx           ← Flash messages
        ├── hooks/
        │   └── useTrans.ts                ← Manual approach hook (if no package)
        └── pages/
            └── dashboard.tsx              ← Use { t } from useLaravelReactI18n
```

---

## 19. Troubleshooting

| Problem | Cause | Solution |
|---------|-------|----------|
| `t()` returns the key as-is | JSON file missing or key doesn't exist | Check `lang/en.json` exists and the key matches exactly |
| Locale not changing after clicking switcher | SetLocale middleware not registered | Add `SetLocale::class` to `bootstrap/app.php` |
| `HandleInertiaRequests` not found | Not installed/registered | In starter kit it's at `app/Http/Middleware/HandleInertiaRequests.php` |
| `locale` is undefined in React | Not shared in `HandleInertiaRequests` | Add `'locale' => App::getLocale()` to `share()` method |
| Translations not loading at all | Vite plugin not added | Add `i18n()` to `vite.config.ts` plugins |
| Changes don't reflect | Config cache is stale | Run `php artisan config:clear && npm run build` |
| RTL layout broken for Urdu/Arabic | `dir` attribute not set | Add `dir={locale === 'ur' \|\| locale === 'ar' ? 'rtl' : 'ltr'}` to your root element |
| Can't find `Kernel.php` | You're on Laravel 12 | There is no Kernel.php in Laravel 12 — use `bootstrap/app.php` |
| `php artisan lang:add` not found | Publisher not installed | Run `composer require laravel-lang/publisher --dev` |
| Validation errors not translated | Language file missing for locale | Run `php artisan lang:add ur` |

---

## 20. Which Approach Should You Use?

```
Are you using Laravel 12 React Starter Kit?
│
├── YES
│   ├── Do you want the simplest solution?
│   │   └── ✅ Use laravel-react-i18n package (Sections 6–12)
│   │       npm install laravel-react-i18n
│   │       Wrap app.tsx with LaravelReactI18nProvider
│   │       Use { t } = useLaravelReactI18n() in components
│   │
│   └── Do you want zero npm packages?
│       └── ✅ Use Manual Inertia Props approach (Sections 13–14)
│           Share translations in HandleInertiaRequests.php
│           Create useTrans() hook in React
│
└── NO (using Blade only)
    └── ✅ Use Laravel's built-in __() helper (classic Blade approach)
        php artisan lang:publish
        Create lang/ur/messages.php
        Use {{ __('messages.key') }} in Blade
```

---

## Quick-Start: Laravel 12 + React in 6 Commands

```bash
# 1. Create app with React starter kit
laravel new my-app --using=react && cd my-app

# 2. Publish lang folder + install translations
php artisan lang:publish
composer require laravel-lang/lang laravel-lang/publisher --dev
php artisan lang:add ur fr

# 3. Install React i18n package
npm install laravel-react-i18n

# 4. Create SetLocale middleware
php artisan make:middleware SetLocale
# Edit the file, then add it to bootstrap/app.php

# 5. Edit these 3 files:
#    - vite.config.ts          → add i18n() plugin
#    - resources/js/app.tsx    → wrap with LaravelReactI18nProvider
#    - app/Http/Middleware/HandleInertiaRequests.php → share 'locale'

# 6. Start developing
composer run dev   # Starts PHP server + Vite together
```

---

*Guide written for **Laravel 12** with the official **React Starter Kit** (Inertia 2, React 19, TypeScript, Tailwind 4, shadcn/ui).*
*All commands tested and working as of 2025.*

---

> 📌 **Tip**: Bookmark this file and refer to it whenever you build something new in Laravel. Every concept here is something you will encounter in real-world production projects.

> 🔗 **Official Docs**: [https://laravel.com/docs](https://laravel.com/docs)
> 🔗 **Laracasts**: [https://laracasts.com](https://laracasts.com)
> 🔗 **Laravel News**: [https://laravel-news.com](https://laravel-news.com)
> 🔗 **Spatie Packages**: [https://spatie.be/open-source](https://spatie.be/open-source)