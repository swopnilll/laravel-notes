### **Generating a New Model in Laravel**

In Laravel, a **model** is a PHP class that represents a database table. Models are part of the **Eloquent ORM (Object-Relational Mapping)** system in Laravel, which provides an elegant and simple way to interact with the database. Each model corresponds to a table in the database, and Laravel allows you to perform CRUD (Create, Read, Update, Delete) operations on that table using the model.

#### **What Does "Generate a New Model" Mean?**

When you run the following command:

`php artisan make:model Flight`

Laravel generates a new PHP class file in the app/Models directory:

```php
// app/Models/Flight.php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    // You can define properties and methods specific to the Flight model here.
}

```

The newly generated `Flight` model class extends `Illuminate\Database\Eloquent\Model`, meaning it inherits all the functionality of the Eloquent ORM, such as querying the database and performing various operations on the `flights` table.

By convention, Laravel assumes:

-   The model name (`Flight`) is singular.
-   The corresponding table name (`flights`) is plural.

This means Laravel will automatically associate the `Flight` model with a table named `flights` unless you explicitly specify a different table name.

* * * * *

### **Using the Model**

Once the model is created, you can start using it to interact with the `flights` table. For example:

1.  **Retrieve All Records**:

```php
use App\Models\Flight;

$flights = Flight::all();  // Fetches all records from the 'flights' table.
```

2.  **Insert a New Record:**:

```php
$flight = new Flight();
$flight->name = 'Flight 101';
$flight->destination = 'New York';
$flight->save();
```


3.  **Update a Record:**:

```php
$flight = Flight::find(1); // Find the record with ID 1
$flight->destination = 'Los Angeles';
$flight->save();
```

4.  **Delete a Record:**:

```php
$flight = Flight::find(1);
$flight->delete();
```


### **Generating a Database Migration When You Generate the Model**

When you generate a model, you often need a corresponding table in the database. To avoid manually creating a migration file separately, Laravel provides the option to automatically generate a migration along with the model by using the `--migration` or `-m` flag:


`php artisan make:model Flight --migration`

This command generates:

1.  **The Model** (`app/Models/Flight.php`):

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model
{
    // The model definition
}
```

2. **The Migration File** (`database/migrations/2024_01_01_000000_create_flights_table.php`):

```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up()
    {
        Schema::create('flights', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('destination');
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('flights');
    }
};

```

#### **Breaking Down the Migration**

1.  **up() Method**:\
    The `up()` method defines the changes to apply to the database. In this case, it creates a `flights` table with:

    -   An `id` column (primary key).
    -   A `name` column (string).
    -   A `destination` column (string).
    -   `created_at` and `updated_at` timestamps (added by `timestamps()`).
2.  **down() Method**:\
    The `down()` method defines how to rollback the changes made by the `up()` method. Here, it drops the `flights` table.

* * * * *

### **Running the Migration**

After generating the migration, you can run it using the `migrate` command:


`php artisan migrate`

This command applies all pending migrations and creates the `flights` table in the database.

