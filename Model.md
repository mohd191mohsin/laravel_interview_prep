# Laravel 12 -- Eloquent ORM Interview Master Guide

## 1. What is Eloquent ORM?

**Definition:**\
Eloquent is Laravel's Active Record implementation that provides an
elegant, expressive interface for interacting with database tables as
PHP objects.

In Laravel 12, Eloquent remains the default ORM and is tightly
integrated with: - Service Container - Query Builder - Model Factories -
Events - Queues - API Resources

Each database table corresponds to a Model class.

Example:

``` php
class User extends Model {}
```

------------------------------------------------------------------------

## 2. What is Active Record Pattern?

Active Record is a design pattern where: - Each model represents a
database table. - Each instance represents a row. - The model contains
both data and database interaction logic.

Example:

``` php
$user = User::find(1);
$user->name = "Mohsin";
$user->save();
```

------------------------------------------------------------------------

## 3. How does Eloquent differ from Query Builder?

| Feature                | Eloquent ORM                          | Query Builder                  |
|------------------------|----------------------------------------|--------------------------------|
| Abstraction Level      | Higher (Model-based)                   | Lower (Table-based)            |
| Usage Style            | Active Record Pattern                  | Fluent Query Builder           |
| Returns                | Model Instances                        | StdClass Objects               |
| Relationships          | Built-in (hasMany, belongsTo, etc.)    | Manual Joins Required          |
| Model Events           | Supported (creating, updating, etc.)   | Not Supported                  |
| Accessors/Mutators     | Supported                              | Not Available                  |
| Global/Local Scopes    | Supported                              | Not Available                  |
| Performance            | Slightly heavier                       | Slightly faster                |
| Use Case               | Business logic & domain modeling       | Complex or optimized queries   |

Query Builder:

``` php
DB::table('users')->where('id', 1)->first();
```

Eloquent:

``` php
User::where('id', 1)->first();
```

------------------------------------------------------------------------

## 4. What is the default table naming convention?

-   Model: `User`
-   Table: `users`

Laravel automatically pluralizes model names.

Override:

``` php
protected $table = 'my_users';
```

------------------------------------------------------------------------

## 5. What are \$fillable and \$guarded?

### Mass Assignment Protection

**$fillable** – allowed columns **$guarded** -- blocked columns

Example:

``` php
protected $fillable = ['name', 'email'];
```

If not defined, mass assignment may throw: `MassAssignmentException`

------------------------------------------------------------------------

## 6. What are Eloquent Relationships?

### Types:

1.  One to One
2.  One to Many
3.  Many to Many
4.  Has Many Through
5.  Polymorphic


### One to One Relationship

### Scenario:

Each User has one Profile.

#### Tables

users: - id - name - email

profiles: - id - user_id - phone - address

#### User Model

``` php
class User extends Model
{
    public function profile()
    {
        return $this->hasOne(Profile::class);
    }
}
```

#### Profile Model

``` php
class Profile extends Model
{
    public function user()
    {
        return $this->belongsTo(User::class);
    }
}
```

#### Usage

``` php
$user = User::with('profile')->find(1);
echo $user->profile->phone;
```

------------------------------------------------------------------------

### One to Many Relationship

#### Scenario:

One User has many Posts.

#### Tables

posts: - id - user_id - title - content

#### User Model

``` php
class User extends Model
{
    public function posts()
    {
        return $this->hasMany(Post::class);
    }
}
```

#### Post Model

``` php
class Post extends Model
{
    public function user()
    {
        return $this->belongsTo(User::class);
    }
}
```

#### Usage

``` php
$user = User::with('posts')->find(1);

foreach ($user->posts as $post) {
    echo $post->title;
}
```

------------------------------------------------------------------------

### Many to Many Relationship

#### Scenario:

Users can have multiple Roles. Roles can belong to multiple Users.

#### Pivot Table

role_user: - user_id - role_id

#### User Model

``` php
class User extends Model
{
    public function roles()
    {
        return $this->belongsToMany(Role::class);
    }
}
```

#### Role Model

``` php
class Role extends Model
{
    public function users()
    {
        return $this->belongsToMany(User::class);
    }
}
```

#### Operations

Attach:

``` php
$user->roles()->attach($roleId);
```

Detach:

``` php
$user->roles()->detach($roleId);
```

Sync:

``` php
$user->roles()->sync([1, 2, 3]);
```

------------------------------------------------------------------------

### Has Many Through

#### Scenario:

Country → Users → Posts

#### Country Model

``` php
class Country extends Model
{
    public function posts()
    {
        return $this->hasManyThrough(Post::class, User::class);
    }
}
```

#### Usage

``` php
$country = Country::find(1);
$posts = $country->posts;
```

------------------------------------------------------------------------

### Polymorphic Relationship

#### Scenario:

Posts and Videos can have Comments.

### comments table:

-   id
-   commentable_id
-   commentable_type
-   content

### Comment Model

``` php
class Comment extends Model
{
    public function commentable()
    {
        return $this->morphTo();
    }
}
```

#### Post Model

``` php
class Post extends Model
{
    public function comments()
    {
        return $this->morphMany(Comment::class, 'commentable');
    }
}
```

#### Video Model

``` php
class Video extends Model
{
    public function comments()
    {
        return $this->morphMany(Comment::class, 'commentable');
    }
}
```


------------------------------------------------------------------------

## 7. What is Lazy Loading vs Eager Loading?

### Lazy Loading (N+1 Problem Risk)

``` php
$users = User::all();
foreach ($users as $user) {
    echo $user->posts;
}
```

### Eager Loading

``` php
User::with('posts')->get();
```

### Prevent Lazy Loading (Laravel 12 Best Practice)

``` php
Model::preventLazyLoading();
```

------------------------------------------------------------------------

## 8. What are Accessors and Mutators?

### Accessor (Get)

``` php
protected function name(): Attribute
{
    return Attribute::make(
        get: fn ($value) => ucfirst($value),
    );
}
```

### Mutator (Set)

``` php
protected function password(): Attribute
{
    return Attribute::make(
        set: fn ($value) => bcrypt($value),
    );
}
```

------------------------------------------------------------------------

## 9. What are Global Scopes?

Automatically applied query constraints.

Example:

``` php
protected static function booted()
{
    static::addGlobalScope('active', function ($query) {
        $query->where('status', 1);
    });
}
```

Remove:

``` php
User::withoutGlobalScope('active')->get();
```

------------------------------------------------------------------------

## 10. What are Local Scopes?

Reusable query logic.

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

## 11. What are Model Events?

Events: - creating - created - updating - updated - deleting - deleted

Example:

``` php
protected static function booted()
{
    static::creating(function ($user) {
        $user->uuid = Str::uuid();
    });
}
```

------------------------------------------------------------------------

## 12. What is Soft Delete?

Adds `deleted_at` column instead of permanent deletion.

``` php
use SoftDeletes;
```

Query:

``` php
User::withTrashed()->get();
```

------------------------------------------------------------------------

## 13. What are Casts in Laravel 12?

Casts convert attributes automatically.

``` php
protected $casts = [
    'is_admin' => 'boolean',
    'meta' => 'array',
    'created_at' => 'datetime',
];
```

Custom Cast:

``` php
php artisan make:cast JsonCast
```

------------------------------------------------------------------------

## 14. What is Attribute Casting vs Accessor?

  Casting              Accessor
  -------------------- -------------------------------
  Simple conversion    Custom logic
  Defined in \$casts   Defined using Attribute class
  Automatic            More flexible

------------------------------------------------------------------------

## 15. How does Eloquent handle transactions?

``` php
DB::transaction(function () {
    User::create([...]);
    Profile::create([...]);
});
```

Used for atomic operations.

------------------------------------------------------------------------

## 16. What is Chunking in Eloquent?

Used for large datasets.

``` php
User::chunk(100, function ($users) {
    foreach ($users as $user) {
        //
    }
});
```

Memory efficient.

------------------------------------------------------------------------

## 17. What is Cursor in Laravel 12?

Uses generators (lazy collections).

``` php
foreach (User::cursor() as $user) {
    //
}
```

Best for millions of records.

------------------------------------------------------------------------

## 18. How to optimize Eloquent queries?

-   Use eager loading
-   Select only required columns
-   Add indexes
-   Avoid N+1
-   Use chunk/cursor
-   Cache heavy queries
-   Use raw queries for complex joins

------------------------------------------------------------------------

## 19. What is the difference between first(), firstOrFail(), find(), findOrFail()?

  Method          Behavior
  --------------- ------------------
  first()         Returns null
  firstOrFail()   Throws 404
  find()          By primary key
  findOrFail()    404 if not found

------------------------------------------------------------------------

## 20. How to override primary key?

``` php
protected $primaryKey = 'uuid';
public $incrementing = false;
protected $keyType = 'string';
```

------------------------------------------------------------------------

# Advanced Interview Scenario Questions

### Q1: How would you prevent N+1 in production?

Answer: - Enable preventLazyLoading() - Use withCount() - Monitor using
Telescope

### Q2: How would you scale Eloquent for high traffic?

Answer: - Read/write DB separation - Redis caching - Query
optimization - Queue heavy tasks - Indexing strategy

------------------------------------------------------------------------

# Conclusion

Eloquent ORM in Laravel 12 is powerful, expressive, and scalable.\
Understanding internal behavior, performance implications, and
architectural best practices is critical for senior-level interviews.
