# Introduction 

 We setup a laravel app in our local system and setup .env file with database credentials. 

 ```js
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=your_database
DB_USERNAME=your_username
DB_PASSWORD=your_password
 ```


**Migrations and Models**
-------------------------

## Laravel's Default User Model and Migration

When you create a new Laravel project, it automatically includes a `User` model and a migration file for the `users` table. These are found in:

-   **Model**: `app/Models/User.php`
-   **Migration**: `database/migrations/xxxx_xx_xx_create_users_table.php`

### Default `users` Table Structure

Running `php artisan migrate` creates a `users` table with the following default columns:

-   `id`: Primary key (auto-increment integer).
-   `name`: User's name (string).
-   `email`: User's email address (unique string).
-   `email_verified_at`: Timestamp for email verification.
-   `password`: Hashed password (string).
-   `remember_token`: Token for "remember me" functionality.
-   `created_at` and `updated_at`: Timestamps for record creation and update.

### Example Migration File

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateUsersTable extends Migration
{
    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('email')->unique();
            $table->timestamp('email_verified_at')->nullable();
            $table->string('password');
            $table->rememberToken();
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('users');
    }
}

```

## Routes

**`routes/api.php`**

-   **Use Case**: For API-based routes that typically do not require sessions or CSRF protection.

-   **Features**:
    -   Designed for stateless communication.
    -   Middleware like `api` is applied by default, which optimizes these routes for API responses (e.g., JSON responses).
    -   Ideal for mobile apps, frontend frameworks (e.g., React, Angular), or third-party integrations consuming your application's API.

-   **No Session Handling**:
    -   API routes are stateless, meaning there's no built-in session or authentication handling like with `web.php`.
    -   Any state management or authentication must be explicitly defined (e.g., using JWT or tokens).

-   **Examples**:

```php
Route::post('/register', [UserController::class, 'register']);
Route::get('/users', [UserController::class, 'index']);
```

### **Middleware in `api.php` Routes**

In Laravel, middleware is a layer of code that sits between an incoming HTTP request and your application's response. It is used to handle common tasks like authentication, logging, or request modification. Middleware for `api.php` routes is optimized for API behavior.

* * * * *

### **Default Middleware: `api`**

#### **1\. Features**

-   The `api` middleware group is applied to all routes defined in `api.php` by default. You can find its configuration in the `Kernel.php` file located in the `app/Http` directory.
-   **Excludes**:
    -   **Session Handling**: APIs are stateless, meaning they do not store user sessions on the server.
    -   **CSRF Protection**: Since APIs are accessed programmatically (e.g., by mobile apps or frontend apps), they do not use forms and do not require CSRF tokens.
-   **Optimizations**:
    -   Focuses on **lightweight communication** by handling JSON requests and responses efficiently.

* * * * *

### **2\. Flow of Middleware**

Here's how a request passes through middleware in `api.php`:

1.  **Incoming Request**:

    -   A client (e.g., React frontend or a mobile app) sends an HTTP request to an endpoint defined in `api.php`.
2.  **Middleware Applied**:

    -   The request passes through the `api` middleware group.
    -   Middleware performs predefined tasks, such as verifying the request format, validating authentication tokens, or applying rate limiting.
3.  **Route Handling**:

    -   Once middleware checks are complete, the request is routed to the specified controller or closure.
4.  **Response**:

    -   The controller processes the request and returns a response (usually JSON).
    -   The response is sent back to the client.

### **3\. Middleware Example**

#### **`Kernel.php` Configuration**

```php
protected $middlewareGroups = [
    'api' => [
        \Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class,
        'throttle:api',
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ],
];

```
#### **Explanation**:

-   **`EnsureFrontendRequestsAreStateful`**: Manages stateful API requests (optional, mainly for SPA applications).
-   **`throttle:api`**: Limits the number of requests a client can make to prevent abuse. By default, it allows 60 requests per minute per user.
-   **`SubstituteBindings`**: Automatically resolves route model bindings.

* * * * *

### **4\. Middleware Code Example**

#### **Custom Middleware**

You can create middleware using the Artisan command

```php
php artisan make:middleware EnsureTokenIsValid
```

##### Middleware Code
```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class EnsureTokenIsValid
{
    public function handle(Request $request, Closure $next)
    {
        // Check if the 'Authorization' header exists and has a valid token
        if ($request->header('Authorization') !== 'Bearer valid-token') {
            return response()->json(['error' => 'Unauthorized'], 401);
        }

        // Allow the request to proceed
        return $next($request);
    }
}

```


#### Register Middleware

Add the middleware to the Kernel.php file:

```php
protected $routeMiddleware = [
    'valid.token' => \App\Http\Middleware\EnsureTokenIsValid::class,
];
```

#### Apply Middleware to a Route

```php
Route::middleware('valid.token')->get('/protected', function () {
    return response()->json(['message' => 'Access granted']);
});

```

