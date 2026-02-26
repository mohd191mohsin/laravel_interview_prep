# Laravel 12 - Eloquent ORM (Model) Interview Questions & Answers

------------------------------------------------------------------------

## 1. What is Eloquent ORM in Laravel 12?

Eloquent is Laravel's Active Record implementation for working with
databases.\
Each database table has a corresponding Model class.

It provides: - Fluent query builder - Relationship handling - Mutators &
accessors - Scopes - Events - Soft deletes

Example:

``` php
class User extends Model {}
```

------------------------------------------------------------------------

## 2. How do you create a Model in Laravel 12?

``` bash
php artisan make:model Product
```

With migration:

``` bash
php artisan make:model Product -m
```

------------------------------------------------------------------------

## 3. What is \$fillable and \$guarded?

### \$fillable

Defines mass-assignable attributes.

``` php
protected $fillable = ['name', 'price'];
```

### \$guarded

Defines protected attributes.

``` php
protected $guarded = ['id'];
```

Best Practice: Prefer \$fillable for security clarity.

------------------------------------------------------------------------

## 4. What is Mass Assignment?

Mass assignment allows creating/updating models using arrays.

``` php
Product::create([
    'name' => 'Laptop',
    'price' => 50000
]);
```

You must define \$fillable to avoid MassAssignmentException.

------------------------------------------------------------------------

## 5. What are Eloquent Relationships?

Types:

-   One to One
-   One to Many
-   Many to Many
-   Has Many Through
-   Polymorphic

Example:

``` php
public function posts()
{
    return $this->hasMany(Post::class);
}
```

------------------------------------------------------------------------

## 6. What is Lazy Loading vs Eager Loading?

### Lazy Loading

Loads relationship when accessed.

``` php
$user->posts;
```

### Eager Loading

Loads relationship with main query.

``` php
User::with('posts')->get();
```

Eager loading prevents N+1 query problem.

------------------------------------------------------------------------

## 7. What are Accessors and Mutators?

Accessors modify data when retrieving. Mutators modify data before
saving.

Example:

``` php
protected function name(): Attribute
{
    return Attribute::make(
        get: fn ($value) => ucfirst($value),
        set: fn ($value) => strtolower($value)
    );
}
```

------------------------------------------------------------------------

## 8. What are Query Scopes?

### Local Scope

``` php
public function scopeActive($query)
{
    return $query->where('status', 1);
}
```

Usage:

``` php
User::active()->get();
```

------------------------------------------------------------------------

## 9. What is Soft Delete?

Adds deleted_at column instead of permanent deletion.

``` php
use SoftDeletes;
```

Restore:

``` php
User::restore();
```

------------------------------------------------------------------------

## 10. What are Model Events?

Lifecycle hooks:

-   creating
-   created
-   updating
-   updated
-   deleting
-   deleted

Example:

``` php
protected static function booted()
{
    static::creating(function ($model) {
        $model->uuid = Str::uuid();
    });
}
```

------------------------------------------------------------------------

## 11. What is the Difference Between first() and get()?

first() → Returns single model instance\
get() → Returns collection

------------------------------------------------------------------------

## 12. What is Chunking in Eloquent?

Used for memory-efficient large data processing.

``` php
User::chunk(100, function ($users) {
    foreach ($users as $user) {
        //
    }
});
```

------------------------------------------------------------------------

## 13. What is Cursor in Laravel 12?

Memory-optimized iteration using generators.

``` php
foreach (User::cursor() as $user) {
    //
}
```

Better for very large datasets.

------------------------------------------------------------------------

## 14. How to Prevent N+1 Query Problem?

Use:

``` php
User::with('posts')->get();
```

Or:

``` php
Model::preventLazyLoading();
```

------------------------------------------------------------------------

## 15. Difference Between Query Builder and Eloquent?

Eloquent: - Model-based - Relationship support - Events

Query Builder: - Faster for raw queries - No model hydration

------------------------------------------------------------------------

## 16. Advanced Scenario Question

How would you optimize a dashboard that loads: - Users - Orders -
Payments - Roles

Answer: - Use eager loading - Use select() for specific columns - Use
caching - Use pagination - Avoid N+1 queries - Use indexes at DB level

------------------------------------------------------------------------

# End of Document
