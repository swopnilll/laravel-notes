# Laravel Query Builder Notes

Reference: https://laravel.com/docs/11.x/queries

## Introduction
Laravel's database query builder provides a convenient, fluent interface for creating and running database queries. It supports various database systems and uses **PDO parameter binding** to prevent SQL injection attacks.

- **No need for string sanitization**: Since the query builder uses PDO parameter binding, there is no need to manually clean or sanitize input strings.
- **Column Binding Limitation**: PDO does not support binding column names. Therefore, user input should never be used directly for column names, including columns used in `ORDER BY` clauses.

---

## Running Database Queries

### Retrieving All Rows From a Table
You can retrieve all rows from a table using the `table` method provided by the `DB` facade. This method returns a fluent query builder instance for the specified table.

#### Example Code
```php
namespace App\Http\Controllers;

use Illuminate\Support\Facades\DB;
use Illuminate\View\View;

class UserController extends Controller
{
    /**
     * Show a list of all of the application's users.
     */
    public function index(): View
    {
        $users = DB::table('users')->get();

        return view('user.index', ['users' => $users]);
    }
}
```

### Explanation
1. **DB::table('users')**:
   - Starts a new query on the `users` table.
2. **get()**:
   - Executes the query and retrieves all rows from the table.
3. **Return Type**:
   - The `get` method returns an instance of `Illuminate\Support\Collection`.
   - Each row is represented as an instance of PHP's `stdClass` object.

### Accessing Column Values
You can access the values of each column by treating them as properties of the `stdClass` object.

#### Example Code
```php
use Illuminate\Support\Facades\DB;

$users = DB::table('users')->get();

foreach ($users as $user) {
    echo $user->name;
}
```

---

## Key Points Summary
- **Fluent Interface**: The `DB` facade's `table` method allows you to chain multiple query constraints.
- **Collection**: The `get` method returns a Laravel collection containing all rows.
- **Secure by Default**: Since the query builder uses PDO parameter binding, SQL injection risks are minimized.
- **Avoid Binding Column Names**: Always hard-code column names in queries to avoid potential security issues.

