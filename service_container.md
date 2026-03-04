Laravel Service Container ek powerful dependency injection container hai jo classes ko manage aur resolve karta hai.
Simple words me:
``` php
Service Container ka kaam hai objects ko automatically create karna aur unki dependencies inject karna.
```
Matlab agar ek class ko dusri class ki zarurat hai, to Laravel automatically us class ka object bana kar inject kar deta hai.


### Create Logger class
```php
class Logger
{
    public function log($message)
    {
        echo "Log: " . $message;
    }
}
```
### Create user controller
```php
class UserController
{
    protected $logger;

    public function __construct(Logger $logger)
    {
        $this->logger = $logger;
    }

    public function store()
    {
        $this->logger->log("User created");
    }
}
```
