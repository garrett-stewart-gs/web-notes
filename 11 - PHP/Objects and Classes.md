## Objects and Classes

### Classes
* a blueprint for making objects
* has a constructor that is called whenever a new object of that class is instantiated ```public function __construct($property1, $property2)```
* has properties (variables)
* has methods (functions)
* methods and properties of instantiated classes (ie. objects) can be accessed with '->' operator
* static methods and properties of non-instantiated classes can be accessed with '::' operator
* public properties and methods must be declared with 'public' keyword
* private properties and methods must be declared with 'private' keyword

### Visibility for Properties and Methods
1. Public = accessible everywhere
2. Protected = accessible with in the class + subclasses
3. Private = accessible only within the class

** WHAT IS MORE COMMON? PROTECTED OR PRIVATE?

### Inheritance
* Classes can belong to other Classes
* 'Sub-classes' can be created using the 'extends' keyword
* a 'Sub-class' inherits the properties and methods of the 'parent-class'
```php
// inheritance
class Vehicle {
    protected $speed;

    public function setSpeed($speed) {
        $this->speed = $speed;
    }

    public function getSpeed() {
        return $this->speed;
    }
}

class Bike extends Vehicle {
    public function displaySpeed() {
        return "The bike's speed is $this->speed km/h.";
    }
}

$bike = new Bike();
$bike->setSpeed(50);
echo $bike->displaySpeed(); // Outputs: The bike's speed is 50 km/h.

```

### Polymorphism
* when a child class overrides a parent class' property or method

** can polymorphism apply to both properties and methods? or only methods?
```php
// polymorphism
class ParentClass {
    public function greet() {
        return "Hello from the parent class.";
    }
}

class ChildClass extends ParentClass {
    public function greet() {
        return "Hello from the child class.";
    }
}

$child = new ChildClass();
echo $child->greet(); // Outputs: Hello from the child class.

```

### Objects
* an instantiated class
* a constructor function call is made using the 'new' keyword


