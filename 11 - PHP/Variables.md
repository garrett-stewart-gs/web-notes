## Variables

### Rules
* must have '$' prepended onto the variable name
* can prepend '&' onto variable reference to pass a variable by reference
* variables are dynamically typed, so you do not specify the type
* variables are dynamic by default, can be set to constant using define() function

### Constant Variables
* in php, it is uncommon to use constant variables, unlike javascript
* variables can be strictly constant using the 'const' keyword or the define() function
* constants cannot be declared inside a code block
```php
define("MEATBAG", "Yes");
echo MEATBAG; // Outputs: Yes
```

### Variable Scope
* variables declared outside of a function is global
* global varaibles can be accessed inside a function, using the 'global' keyword before referencing the variable name (```echo global $x```)
* 

### Static Scope
* variables inside of a function can persist across function calls using the 'static' keyword
```php
function staticVar() {
    static $count = 0;
    $count++;
    echo $count;
}
staticVar(); // 1
staticVar(); // 2

```

### Type Casting
* You can convert values from one data type to another
```php
$number = (int) "47"; // Converts to integer.
```

### Predefined Variables (SUPER GLOBALS)
* php comes with predefined variables, like: ```$_GET```, ```$_POST```, ```$_SESSION```, and ```$_COOKIE```
