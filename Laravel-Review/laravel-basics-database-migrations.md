# Laravel Basics: Database Migrations

[![hackmd-github-sync-badge](https://hackmd.io/@jmrecodes/SkAqzF17ex/badge)](https://hackmd.io/@jmrecodes/SkAqzF17ex)

Migrations are like version control for your database, allowing your team to easily define and share the application's database schema. They are typically paired with Laravel's schema builder to easily build your application's database schema.

## What are Migrations?

*   **Schema Version Control:** Migrations allow you to define your database table structure (schema) in PHP code.
*   **Collaboration:** They make it easy for teams to keep their database schemas synchronized.
*   **Incremental Changes:** Each migration file represents a change to the database (e.g., creating a table, adding a column, modifying a column, creating an index).
*   **Location:** Migration files are stored in the `database/migrations` directory. Each migration filename contains a timestamp, which allows Laravel to determine the order of the migrations.

## Generating Migrations

You can create a new migration file using the Artisan `make:migration` command:

```bash
# To create a new table
php artisan make:migration create_flights_table --create=flights

# To modify an existing table (e.g., add a column)
php artisan make:migration add_paid_column_to_flights_table --table=flights
```

*   The `--create=tableName` option pre-fills the migration with code to create the specified table.
*   The `--table=tableName` option pre-fills the migration with code to modify the specified table.

## Migration Structure

A migration class contains two primary methods: `up` and `down`.

*   **`up()` method:** This method is used to add new tables, columns, or indexes to your database. It defines the desired changes to your schema.
*   **`down()` method:** This method should reverse the operations performed by the `up` method. When you roll back a migration, the `down` method is executed.

Here's an example of a migration that creates a `flights` table:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('flights', function (Blueprint $table) {
            $table->id(); // Alias for $table->bigIncrements('id');
            $table->string('name');
            $table->string('airline');
            $table->timestamps(); // Adds nullable created_at and updated_at columns
        });
    }

    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('flights');
    }
};
```

## Schema Builder

Laravel's `Schema` facade provides database agnostic support for creating and manipulating tables across all of Laravel's supported database systems.

### Creating Tables
Use the `create` method on the `Schema` facade, as shown in the example above.

### Updating Tables
Use the `table` method on the `Schema` facade:
```php
Schema::table('users', function (Blueprint $table) {
    $table->string('email')->nullable()->change(); // Modify a column
    $table->integer('votes'); // Add a new column
});
```

### Renaming / Dropping Tables
```php
Schema::rename($from, $to);
Schema::drop('users');
Schema::dropIfExists('users');
```

### Column Types
The schema builder blueprint offers a variety of column types you can use when building your tables:
*   `$table->id();` - Auto-incrementing BIGINT (primary key) equivalent.
*   `$table->bigIncrements('id');`
*   `$table->string('name', 100);` - VARCHAR equivalent with a length.
*   `$table->text('description');` - TEXT equivalent.
*   `$table->integer('votes');`
*   `$table->boolean('confirmed');`
*   `$table->date('created_on');`
*   `$table->dateTime('published_at');`
*   `$table->timestamp('added_on');`
*   `$table->foreignId('user_id')->constrained();` - Shortcut for foreign key creation.
*   And many more...

### Column Modifiers
In addition to column types, there are several "modifiers" you can use:
*   `->nullable()`
*   `->default($value)`
*   `->unsigned()`
*   `->unique()`
*   `->after('column')` (MySQL only)
*   `->comment('Your comment')`

### Indexes
*   `$table->primary('id');`
*   `$table->unique('email');`
*   `$table->index('state');`

## Running Migrations

Once your migrations are created, you run them using the Artisan command:

```bash
php artisan migrate
```
This command runs all of your outstanding migrations (migrations that haven't been run yet).

## Rolling Back Migrations

To roll back the last migration operation (the last "batch" of migrations that ran):
```bash
php artisan migrate:rollback
```
You can roll back a specific number of steps:
```bash
php artisan migrate:rollback --step=5
```
To roll back all migrations:
```bash
php artisan migrate:reset
```
To roll back all migrations and then re-run them (useful for resetting the database during development):
```bash
php artisan migrate:refresh

# To also run seeders after refreshing
php artisan migrate:refresh --seed
```

## Migration Status
To see which migrations have already run:
```bash
php artisan migrate:status
```

---
[Back to Laravel Basics Index](laravel-basics-index.md)

[Back to Knowledge Base Index](/README.md)