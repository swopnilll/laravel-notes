### Running SQL Queries in Laravel Using the `DB` Facade

Reference: https://laravel.com/docs/11.x/database

In Laravel, the `DB` facade is used to interact directly with the database, allowing you to run SQL queries for operations such as `SELECT`, `INSERT`, `UPDATE`, `DELETE`, and general SQL statements. This approach is useful when you need to write raw SQL queries instead of using Eloquent ORM or the query builder.

* * * * *

### **Basic Query Example: SELECT**

The `select` method provided by the `DB` facade allows you to retrieve data from the database. Here's how it works:

```php
$users = DB::select('select * from users where active = ?', [1]);
```

1.  **SQL Query**:\
    The first argument is a raw SQL query:\
    `'select * from users where active = ?'`\
    This query retrieves all records from the `users` table where the `active` column is equal to a specified value.

2.  **Parameter Binding**:\
    The second argument is an array containing the values to bind to the query parameters (`?`). In this case, `[1]` means that the `active` column must have the value `1`.

3.  **SQL Injection Protection**:\
    Laravel automatically binds the parameters securely to prevent SQL injection attacks. This is a safer way of executing queries compared to directly concatenating user input into the query string.

4.  **Return Value**:\
    The `select` method always returns an array of results. Each result is a `stdClass` object representing a row from the database.

Example:

```php
foreach ($users as $user) {
    echo $user->name;
}

```

Here, `$user->name` accesses the `name` column for each user in the result set.

### **Example with API Controller**

Instead of returning a view, you might want to return the data as JSON in an API response. Here's how you can modify the example for an API:

#### **Code: API Example**

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\JsonResponse;
use Illuminate\Support\Facades\DB;

class UserController extends Controller
{
    /**
     * Retrieve a list of active users.
     *
     * @return JsonResponse
     */
    public function getActiveUsers(): JsonResponse
    {
        // Running a SELECT query with parameter binding
        $users = DB::select('select * from users where active = ?', [1]);

        // Returning the results as a JSON response
        return response()->json([
            'success' => true,
            'data' => $users,
        ]);
    }
}

```

### **Key Points**

1.  **Why Use Raw Queries?**\
    While Laravel's Eloquent ORM and query builder provide an elegant way to interact with the database, raw queries using the `DB` facade can be useful when:

    -   You need to execute complex queries that are difficult to express using Eloquent.
    -   Performance optimization requires direct SQL manipulation.
    -   You are migrating legacy code with existing SQL queries.
2.  **Security**:\
    Parameter binding ensures that your application is safe from SQL injection by separating SQL logic from data inputs.

3.  **API Structure**:\
    The API example returns data in a standard JSON format, which is ideal for frontend applications or third-party integrations.


