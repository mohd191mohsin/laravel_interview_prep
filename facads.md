## Facade kya hota hai (Simple Definition)
Facade Laravel ka ek class hota hai jo kisi service ko static style me access karne deta hai.
```php
use Illuminate\Support\Facades\Cache;

Cache::put('name', 'Mohsin', 60);
```

```php
Cache Facade
     ↓
Service Container
     ↓
Cache Manager Object
     ↓
Actual Method Execute
```
