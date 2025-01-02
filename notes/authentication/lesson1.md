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

### Laravel's Default User Model and Migration

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