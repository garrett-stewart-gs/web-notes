## PHP Syntax

### PHP Tags

PHP code is written inside <?php ... ?> tags.

<?php
// Your PHP code here
?>

### Variables

Variables in PHP start with $ and do not require explicit type declarations.

<?php
$name = "John";    // String
$age = 25;         // Integer
$price = 19.99;    // Float
$isAdmin = true;   // Boolean
?>

### Data Types

PHP supports several data types:

  String
  Integer
  Float
  Boolean
  Array
  Object
  NULL

### Output

Use echo or print to output text or variables.

<?php
echo "Hello, World!";
print $name;
?>

### Concatenation

Strings are concatenated using the . operator.

<?php
echo "Hello, " . $name . "!";
?>

### Arrays

Define arrays using array() or [].

<?php
// Indexed array
$colors = ["red", "green", "blue"];
echo $colors[1]; // Outputs: green

// Associative array
$ages = ["John" => 25, "Jane" => 30];
echo $ages["Jane"]; // Outputs: 30
?>

### Conditional Statements

PHP supports if, else, and elseif.

<?php
if ($age >= 18) {
    echo "Adult";
} elseif ($age >= 13) {
    echo "Teenager";
} else {
    echo "Child";
}
?>

### Loops
while Loop

<?php
$i = 1;
while ($i <= 5) {
    echo $i;
    $i++;
}
?>

for Loop

<?php
for ($i = 1; $i <= 5; $i++) {
    echo $i;
}
?>

foreach Loop

<?php
foreach ($colors as $color) {
    echo $color;
}
?>

### Functions

Define functions using the function keyword.

<?php
function greet($name) {
    return "Hello, " . $name;
}
echo greet("John");
?>

### Classes and Objects

Create classes using the class keyword.

<?php
class Person {
    public $name;
    public $age;

    function __construct($name, $age) {
        $this->name = $name;
        $this->age = $age;
    }

    function greet() {
        return "Hello, " . $this->name;
    }
}

$person = new Person("John", 25);
echo $person->greet();
?>

### Superglobals

PHP has several built-in superglobal arrays for managing server, request, and session data:

    $_GET – Contains URL parameters
    $_POST – Contains form data sent via POST
    $_SESSION – Contains session data

<?php
// Accessing query parameters
echo $_GET["name"];

// Accessing form data
echo $_POST["email"];
?>

### Include and Require

Use include or require to include other PHP files.

<?php
include "header.php";
require "config.php";
?>

    include gives a warning if the file is missing.
    require gives a fatal error if the file is missing.