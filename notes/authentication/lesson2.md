# Creating Authentication

### Opening up the register URL

routes/api.php

```php
<?php

use App\Http\Controllers\RegisterController;
use Illuminate\Support\Facades\Route;

Route::post('/register', RegisterController::class);
```

We can directly add a POST route to accept data to register a new user. This will be inside the routes/api.php 


### Handle the requests with a Controller

- Controller takes charge when a particular route is called

```php
<?php

namespace App\Http\Controllers;

use App\Http\Requests\RegisterValidation;
use App\Models\User;

class RegisterController extends Controller
{
    public function __invoke(RegisterValidation $request): void
    {
        User::create($request->validated());
    }
}
```

-   **`use App\Http\Requests\RegisterValidation;`** -- This line imports the `RegisterValidation` class, which is used to handle the validation of the incoming registration data. This class will define the rules that validate the form input before the data is processed.

-   **`use App\Models\User;`** -- This imports the `User` model, which represents the `users` table in the database. This model will be used to interact with the database, such as creating new records in the `users` table when the user registers.

### The Controller Class:

- The RegisterController class extends Laravel's base Controller class. 
- This base class provides common functionality that can be shared across all controllers in the application (such as middleware handling).
- This specific controller handles the registration functionality, as the name RegisterController suggests.

#### The __invoke Magic Method:

```php
public function __invoke(RegisterValidation $request): void
```

-   **Why use `__invoke`?**

    -   Laravel controllers often define named methods (like `store()`, `create()`, etc.), but in this case, since there's only one method, we can define the controller as an "invokable controller." This simplifies things by allowing the controller to be used as a single action. With this approach, Laravel doesn't need to specify the method name in the route (i.e., you can use `Route::post('/register', RegisterController::class)`).-   **The `$request` Parameter:**

    -   The `$request` parameter is an instance of the `RegisterValidation` request class, which handles data validation for the registration form.
    -   The `RegisterValidation` class should extend `Illuminate\Foundation\Http\FormRequest` and define the validation rules for the form data.-   **Return Type:**

    -   The `: void` return type indicates that this method doesn't return anything. It simply performs an action (creating a user) and ends.

#### Data Validation with RegisterValidation:

```php
$request->validated();
```

-   The `RegisterValidation` class handles the **validation logic** for the registration form data.

    -   The `validated()` method on the `$request` object will run the defined validation rules and return only the **validated data**. If the data fails the validation rules, Laravel will automatically return a response with the validation errors, and the registration process will not proceed.

    -   For example, `RegisterValidation` might contain rules like:

```php
public function rules()
{
    return [
        'email' => 'required|email|unique:users,email',
        'password' => 'required|min:8|confirmed',
    ];
}

```

- The data passed to create() will only include those fields that have passed the validation rules.

#### Creating a New User:

```php
User::create($request->validated());
```

-   This line creates a new user in the database using the `User` model's `create()` method.

    -   **`User::create()`** is an **Eloquent** method that allows you to insert data into the `users` table. It takes an array of key-value pairs, where the keys are column names and the values are the corresponding data to insert.

    -   The `$request->validated()` method returns an array of only the valid data, which is then passed to the `create()` method. This ensures that only the validated data (e.g., email, password) is inserted into the `users` table.

    -   **Important:** The `User` model must have the **`fillable`** property set to specify which attributes can be mass-assigned. For example:

```php
class User extends Authenticatable
{
    protected $fillable = ['name', 'email', 'password'];
}

```

This ensures that only name, email, and password can be passed into the create() method.

### **Best Practices and Explanation of Code Structure:**

-   **Single Responsibility Principle (SRP):**

    -   The `RegisterController` does just one thing: it handles the user registration logic. It receives the validated request data and creates a new user in the database.
-   **Invokable Controllers:**

    -   By making the controller "invokable", the code becomes cleaner and more concise. There's no need to define a separate method for handling the logic (such as `store()` or `register()`). This is especially useful when a controller only handles a single action, like registration.
-   **Validation Class:**

    -   Using the `RegisterValidation` request class helps keep the controller clean and separate concerns. The controller only handles the registration logic, while the `RegisterValidation` class focuses on ensuring the incoming data is correct before it's processed.
-   **Efficiency:**

    -   By using the `create()` method on the `User` model, the code leverages **Eloquent's** features to handle data insertion in a simple and efficient manner.

### 8\. **Summary of Flow:**

1.  The route calls the `RegisterController`.
2.  The `__invoke()` method is triggered.
3.  The `RegisterValidation` class validates the incoming request data.
4.  If valid, the `create()` method of the `User` model is called with the validated data.
5.  A new user is created in the database.

### Why this design is preferred:

-   **Clean and simple**: This approach follows the **single responsibility principle**, making each class handle just one aspect of the logic (validation, database interaction, etc.).
-   **Extensibility**: If you want to add more features (like sending a welcome email or logging user activity), you can extend this controller with new methods or services without overcomplicating it.
-   **Readability**: Laravel developers can quickly understand what the controller does: it validates the registration request and creates a user.

In conclusion, this is a clean, scalable, and efficient way to handle user registration in a Laravel application using controllers, validation classes, and Eloquent models.