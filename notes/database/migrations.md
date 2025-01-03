# Migrations

Reference: https://laravel.com/docs/11.x/migrations

### **Understanding Migrations in Laravel**

Migrations in Laravel act as **version control for your database**. They allow developers to define the database schema in a structured and consistent manner using code, ensuring that all team members can keep their local databases in sync. Instead of manually adding or modifying tables and columns in the database, developers write migration files that describe the changes. When these migrations are executed, they update the database accordingly.

* * * * *

### **Why Use Migrations?**

1.  **Version Control**:\
    Migrations enable tracking of database schema changes over time, making it easy to rollback changes or apply them consistently across different environments (development, staging, production).

2.  **Team Collaboration**:\
    With migrations, team members can pull the latest changes and run the migration commands to update their local databases without manual intervention.

3.  **Consistency**:\
    Migrations ensure that changes are applied uniformly across all environments, reducing human error.

* * * * *

### **Generating Migrations**

#### **Example 1: Creating a Table**

You can create a new migration using the make:migration command:

```php
php artisan make:migration create_users_table
```

Laravel automatically generates a file in the database/migrations directory with the following structure:

```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('email')->unique();
            $table->timestamp('email_verified_at')->nullable();
            $table->string('password');
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('users');
    }
};

```


-   **up() method**: Defines the schema changes to apply, in this case, creating a `users` table with columns like `name`, `email`, and `password`.
-   **down() method**: Defines how to revert the changes, in this case by dropping the `users` table.

#### **Running the Migration**

To apply this migration and create the table in the database, run:

```php
php artisan migrate
```

#### **Example 2: Modifying an Existing Table**

Sometimes, you need to add a new column to an existing table. You can generate a migration for this:

bash

Copy code

`php artisan make:migration add_profile_photo_to_users_table --table=users`

This generates a migration file where you can define the changes:

```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up()
    {
        Schema::table('users', function (Blueprint $table) {
            $table->string('profile_photo')->nullable();
        });
    }

    public function down()
    {
        Schema::table('users', function (Blueprint $table) {
            $table->dropColumn('profile_photo');
        });
    }
};

```
-   The `up()` method adds a new nullable column `profile_photo` to the `users` table.
-   The `down()` method removes the `profile_photo` column, ensuring the migration is reversible.

After writing this migration, run:

`php artisan migrate`

### **Real Application Development Use Case**

#### **Scenario**: E-commerce Application

1.  **Initial Setup**:\
    When starting an e-commerce project, you may create migrations for essential tables like `products`, `categories`, and `orders`.

```php
    php artisan make:migration create_products_table
    php artisan make:migration create_categories_table
    php artisan make:migration create_orders_table
```


2.  **Defining the Schema**:

```php
// create_products_table migration
    Schema::create('products', function (Blueprint $table) {
        $table->id();
        $table->string('name');
        $table->text('description');
        $table->decimal('price', 8, 2);
        $table->timestamps();
    });
```

```php
    // create_categories_table migration
    Schema::create('categories', function (Blueprint $table) {
        $table->id();
        $table->string('name');
        $table->timestamps();
    });
```

```php
    // create_orders_table migration
    Schema::create('orders', function (Blueprint $table) {
        $table->id();
        $table->unsignedBigInteger('user_id');
        $table->decimal('total_price', 10, 2);
        $table->timestamps();
    });
```

3.  **Adding a New Feature**:\
    Later, you decide to add a feature that allows users to upload product images. Instead of manually altering the `products` table, you create a migration:


    `php artisan make:migration add_image_column_to_products_table --table=products`

    In the generated migration:

```php
    Schema::table('products', function (Blueprint $table) {
        $table->string('image_path')->nullable();
    });
```

4.  **Applying Migrations in Different Environments**:\
    When deploying the application to production, simply run:

    `php artisan migrate`

    This ensures the production database is updated with all the schema changes made during development.

* * * * *

### **Conclusion**

Laravel migrations streamline the process of managing database schema changes, making it easier for teams to collaborate and maintain consistency. By defining schema changes as code, migrations reduce manual errors and allow for easy rollback if needed. This is especially valuable in real-world applications where schema changes occur frequently during development.


