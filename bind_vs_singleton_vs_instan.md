## Laravel Service Container – Important Binding Methods

Laravel Service Container mein **3 important binding methods** hoti hain jo decide karti hain ki object kaise create aur manage hoga.

| Method     | Object Creation Behavior |
|------------|--------------------------|
| `bind`     | Har request par **naya object create** hota hai |
| `singleton`| **Pure application lifecycle mein ek hi object** reuse hota hai |
| `instance` | **Already created object** container mein register kiya jata hai |
