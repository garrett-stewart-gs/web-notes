## PHP Functions

### Defining Functions
* same as most C-based languages
```php
function greet($name = "Meatbag") { // sets default value to variable, in case user doesn't pass anything
    return "Hello, $name!";
}

// Type declarations (similar to type safety, but not as strict)
// this function declares that the 2 variables $a and $b should be integers
// this function also declares that it should return an integer with ': int'
function add(int $a, int $b): int {
    return $a + $b;
}

```

### Closures
* a function that can capture variables from its surrounding scope
* an anonymous function that was created using the 'function' keyword
```php
$x = 10;

$closure = function() use ($x) {
    $x++;
    echo $x; // Outputs: 11
};

$closure();
echo $x; // Outputs: 10 (original $x is unchanged, unless $x is passed by reference: '&$x')

```

### Arrow Functions
* reduced syntax
* can only be 1 line long (unlike js)
* return statement is implied, you NEVER use the 'return' keyword with an arrow function
* no name = anonymous
* it is a 'closure', but you do not need to use the keyword 'use' to capture a specific variable
* has access to all the variables in its parent's scope, including '$this' (only if arrow function is used inside a class)
* '$this' does not need to be specified in the function declaration to be included in the closure/scope
```php
$multiply = fn($x, $y) => $x * $y;

$x = 10;
$arrow = fn($y) => $x + $y; // $x is captured from the parent scope

echo $arrow(5); // Outputs: 15
```


### Static Functions
* functions that belong to a class, where the class does not need to be instantiated as an object
* does not have access to ```$this```, because it does not rely on being instantiated (ie. doesn't have context)
```php
class Math {
    public static function square($n) {
        return $n * $n;
    }
}

$result = Math::square(4); // the class method is called, but Math hasn't been instantiated

```

### Passing Variables
* There are 2 ways to pass a variable to a function:
* 1. By Value
* 2. By Reference (using '&')
