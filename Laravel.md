# **Laravel 12 – The Complete Guide**

A beautifully structured and comprehensive tutorial for mastering **Laravel 12**. This document is organized with clean Markdown, enhanced with minimal HTML/CSS styling for clarity.

---

<style>
.section-title {
  background: #f5f5f5;
  padding: 10px;
  border-left: 4px solid #4F46E5;
  font-size: 1.4rem;
  font-weight: bold;
  margin-top: 40px;
}
.code-block {
  background: #1e1e1e;
  color: #dcdcdc;
  padding: 15px;
  border-radius: 6px;
  font-size: 0.9rem;
  overflow-x: auto;
}
.note {
  padding: 10px;
  background: #eef7ff;
  border-left: 4px solid #2196F3;
  margin: 15px 0;
}
.warning {
  padding: 10px;
  background: #fff3cd;
  border-left: 4px solid #ff9800;
  margin: 15px 0;
}
</style>

---

# **Table of Contents**

1. [Introduction to Laravel](#1-introduction-to-laravel)
2. [Installing Laravel](#2-installing-laravel)
3. [Laravel Directory Structure](#3-laravel-directory-structure)
4. [Routing in Laravel](#4-routing-in-laravel)
5. [Controllers](#5-controllers)
6. [Views (Blade Templates)](#6-views-blade-templates)
7. [Models & Eloquent ORM](#7-models--eloquent-orm)
8. [Database Migrations](#8-database-migrations)
9. [Database Seeders & Factories](#9-database-seeders--factories)
10. [Request Lifecycle](#10-request-lifecycle)
11. [Middleware](#11-middleware)
12. [Authentication (Laravel Breeze / Fortify)](#12-authentication-laravel-breeze--fortify)
13. [Authorization (Gates & Policies)](#13-authorization-gates--policies)
14. [API Development with Laravel](#14-api-development-with-laravel)
15. [Laravel Sanctum Authentication](#15-laravel-sanctum-authentication)
16. [File Uploads](#16-file-uploads)
17. [Queues & Jobs](#17-queues--jobs)
18. [Events & Listeners](#18-events--listeners)
19. [Notifications](#19-notifications)
20. [Task Scheduling](#20-task-scheduling)
21. [Caching](#21-caching)
22. [Eloquent Relationships](#22-eloquent-relationships)
23. [Laravel Collections](#23-laravel-collections)
24. [Laravel Helpers](#24-laravel-helpers)
25. [Service Container & Service Providers](#25-service-container--service-providers)
26. [Laravel Artisan](#26-laravel-artisan)
27. [Laravel Best Practices](#27-laravel-best-practices)
28. [Deployment Guide](#28-deployment-guide)
29. [Common Errors & Fixes](#29-common-errors--fixes)
30. [Full Mini Project Example](#30-full-mini-project-example)

---

## <div class="section-title">1 Introduction to Laravel</div>

Laravel 12 is the latest version of the world’s most popular PHP framework. It offers:

- Modern structure
- MVC architecture
- Built-in authentication
- Queue system
- API tools
- Eloquent ORM
- Blade templating
- Powerful routing

---

## <div class="section-title">2 Installing Laravel</div>

### **Requirements**

- PHP 8.2+
- Composer
- MySQL or PostgreSQL

### **Install via Composer**

```bash
composer create-project laravel/laravel example-app
cd example-app
php artisan serve
```

Visit: **http://localhost:8000**

---

## <div class="section-title">3 Directory Structure</div>

```
app/
 ├─ Http/
 │   ├─ Controllers/
 │   ├─ Middleware/
 ├─ Models/
bootstrap/
config/
database/
resources/
 ├─ views/
routes/
 ├─ web.php
 ├─ api.php
```

Each folder has a specific purpose.

---

## <div class="section-title">4 Routing in Laravel</div>

### **Basic Route**

```php
// routes/web.php
Route::get('/', function () {
    return "Hello Laravel 12";
});
```

### **Route with Controller**

```php
Route::get('/users', [UserController::class, 'index']);
```

---

## <div class="section-title">5 Controllers</div>

Create a controller:

```bash
php artisan make:controller UserController
```

Example controller:

```php
<?php
namespace App\Http\Controllers;

use App\Models\User;

class UserController extends Controller
{
    public function index()
    {
        $users = User::all();
        return view('users.index', compact('users'));
    }
}
```

---

## <div class="section-title">6 Views (Blade Templates)</div>

Basic blade:

```blade
<!-- resources/views/users/index.blade.php -->
@extends('layouts.app')

@section('content')
  <h1>Users</h1>
  <ul>
    @foreach($users as $user)
      <li>{{ $user->name }}</li>
    @endforeach
  </ul>
@endsection
```

---

## <div class="section-title">7 Models & Eloquent ORM</div>

Create a model:

```bash
php artisan make:model User -m
```

Model example:

```php
<?php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    protected $fillable = ['name', 'email'];
}
```

---

## <div class="section-title">8 Database Migrations</div>

Create migration:

```bash
php artisan make:migration create_users_table
```

Migration example:

```php
Schema::create('users', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->string('email')->unique();
    $table->timestamps();
});
```

Run migrations:

```bash
php artisan migrate
```

---

## <div class="section-title">9 Database Seeders & Factories</div>

Create factory:

```bash
php artisan make:factory UserFactory --model=User
```

Factory example:

```php
public function definition()
{
    return [
        'name' => $this->faker->name,
        'email' => $this->faker->unique()->safeEmail,
    ];
}
```

Seeder:

```bash
php artisan db:seed --class=UserSeeder
```

---

## <div class="section-title">10 Request Lifecycle</div>

Brief overview of how HTTP requests are handled: entry via public/index.php → HTTP kernel → middleware → route matching → controller → response.

---

## <div class="section-title">11 Middleware</div>

Create middleware:

```bash
php artisan make:middleware EnsureUserIsAdmin
```

Middleware skeleton:

```php
public function handle($request, Closure $next)
{
    if (! $request->user() || ! $request->user()->is_admin) {
        abort(403);
    }
    return $next($request);
}
```

---

## <div class="section-title">12 Authentication (Laravel Breeze / Fortify)</div>

Install Breeze:

```bash
composer require laravel/breeze --dev
php artisan breeze:install
npm install
npm run dev
php artisan migrate
```

For API authentication consider Laravel Sanctum or Passport.

---

## <div class="section-title">13 Authorization (Gates & Policies)</div>

Create policy:

```bash
php artisan make:policy PostPolicy --model=Post
```

Policy example:

```php
public function update(User $user, Post $post)
{
    return $user->id === $post->user_id;
}
```

Register in AuthServiceProvider.

---

## <div class="section-title">14 API Development with Laravel</div>

Use routes/api.php for API routes and api resources:

```php
Route::apiResource('posts', PostController::class);
```

Resource controller:

```bash
php artisan make:controller Api/PostController --api --model=Post
```

API Resources:

```bash
php artisan make:resource PostResource
```

---

## <div class="section-title">15 Laravel Sanctum Authentication</div>

Install:

```bash
composer require laravel/sanctum
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
php artisan migrate
```

Add middleware 'auth:sanctum' to protected API routes.

---

## <div class="section-title">16 File Uploads</div>

Form:

```html
<form action="/upload" method="POST" enctype="multipart/form-data">
  @csrf
  <input type="file" name="photo" />
  <button>Upload</button>
</form>
```

Controller:

```php
$request->file('photo')->store('photos', 'public');
```

---

<div class="section-title">17. Queues & Jobs</div>

Create job:

```bash
php artisan make:job ProcessPodcast
```

Dispatch:

```php
ProcessPodcast::dispatch($podcast);
```

Use queue drivers like database, redis, or beanstalkd.

---

## <div class="section-title">18 Events & Listeners</div>

Create event and listener:

```bash
php artisan make:event OrderShipped
php artisan make:listener SendShipmentNotification --event=OrderShipped
```

Register in EventServiceProvider.

---

## <div class="section-title">19 Notifications</div>

Create notification:

```bash
php artisan make:notification InvoicePaid
```

Send via:

```php
$user->notify(new InvoicePaid($invoice));
```

Supports mail, database, broadcast, and Slack channels.

---

## <div class="section-title">20 Task Scheduling</div>

Kernel:

```php
protected function schedule(Schedule $schedule)
{
    $schedule->command('emails:send')->daily();
}
```

Run scheduler via cron: `* * * * * php /path/to/artisan schedule:run >> /dev/null 2>&1`

---

## <div class="section-title">21 Caching</div>

Use cache facade:

```php
Cache::put('key', 'value', 3600);
$value = Cache::get('key');
```

Drivers: file, database, redis, memcached.

---

## <div class="section-title">22 Eloquent Relationships</div>

Examples:

```php
class Post extends Model {
  public function comments() {
    return $this->hasMany(Comment::class);
  }
}
```

BelongsTo, HasMany, ManyToMany, HasOne, Morph relations.

---

## <div class="section-title">23 Laravel Collections</div>

Collections utility:

```php
collect([1,2,3])->map(fn($n) => $n * 2)->filter(fn($n) => $n > 2);
```

---

## <div class="section-title">24 Laravel Helpers</div>

Useful helpers: `route()`, `old()`, `asset()`, `mix()`, `dd()`, `collect()`.

---

## <div class="section-title">25 Service Container & Service Providers</div>

Bind interfaces:

```php
$this->app->bind(PaymentGateway::class, StripePaymentGateway::class);
```

Register providers in config/app.php.

---

## <div class="section-title">26 Laravel Artisan</div>

Common commands:

```bash
php artisan migrate
php artisan make:controller
php artisan tinker
php artisan route:list
```

---

## <div class="section-title">27 Laravel Best Practices</div>

- Use form requests for validation
- Keep controllers thin
- Use resource controllers
- Use policies for authorization
- Cache heavy queries
- Use queues for long-running tasks

---

## <div class="section-title">28 Deployment Guide</div>

- Use Env variables
- Optimize config: `php artisan config:cache`
- Optimize routes: `php artisan route:cache`
- Use queues and supervisors for workers
- Use CI/CD pipelines (GitHub Actions, GitLab CI)

---

## <div class="section-title">29 Common Errors & Fixes</div>

- 500 errors: check logs in storage/logs/laravel.log
- Migration issues: `php artisan migrate:status`
- Permission issues: set storage and bootstrap cache writable

---

## <div class="section-title">30 Full Mini Project Example</div>

A short example: a simple blog app routes, controllers, migrations and views — see the examples above combined to build a CRUD blog.

---

# Appendix

- Links to official docs: https://laravel.com/docs
- Commands quick cheatsheet
- Further reading & advanced topics: Horizon, Telescope, Octane, Sail, Livewire, Inertia.js

---

_End of document_
