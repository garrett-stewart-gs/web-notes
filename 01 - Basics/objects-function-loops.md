Objects:

# Primitives
* boolean
* string
* number (NaN is a type of Number, but NaN != NaN) (cannot be larger than Number.MAX_SAFE_INTEGER)
* big int (put "n" at the end of a number)
* undefined
* null
* symbol

### Primitives are cannot be altered (when you re-assign, js destroys the old data, and re-creates it with a different value

# Objects
* dictionaries (python), hashes (ruby), associative array, etc.
* Objects are collections of KEY / VALUE PAIRS
* Objects are passed by reference, so when you pass an object to a function, that function can modify the original version. EVEN IF IT IS A CONST OBJ. (you can change the contents of a const obj, but you can't change the obj itself (ie. can't change to array or variable))

### Datastructures
Arrays and Objects are both data structures. Arrays lack a key/value pair though, you need to reference individual values by an index number. Objects allow for easier retrieval of individual values and more readable code.

#### How to declare objects
Arrays:
```js
const studentOneObj = [];
```
Objects:
```js
const studentOneObj = {
  'key' : value,
  key : value, // you do not need to put quotes around a key if it is characters only (no spaces and not a number). This is actually a standard convention.
}
```

#### Referencing key/value pairs for objects

```js
studentOneObj['key'] = value // this is a re-assignment of the value for the key 'key'. works 100% of the time
```

```js
studentOneObj.key //reads the value held in the 'key' key. 
```

```js
console.log() 
console['log']
```


#### Memory Leak = when an application takes RAM memory but does not give it back when it is done using it.

#### this. (self reference)
'this' is a reference to self. using 'this' allows the object to refer to itself regardless of which variable is referencing the content. (you can have 2 variables pointing ot the same object, but if you refer to 1 specific object name, you will run into errors if you ever remove that specific object name. 'this' bypasses it). 'this' refers to the parent "container/function/whatever". ARROW FUNCTIONS DO NOT CREATE 'THIS'


#### FUNCTION DECLARATIONS
avoid function declarations, because they get HOISTED. LHL wants us to use function expressions, because you cannot hoist function expressions, which means you have to write a function before calling it, which is also a BEST PRACTICE. Other Dev teams may want you to 


## LOOPS
c-style loop (for)
while
for..in (steps through the indexes) // very useful for iterating over objects, which would not be possible with other loops!
for..of (steps through the values)

Special notes: you can loop through an object values using a for..of loop IF you grab all the values using Object.values(objectName), because it returns an array that you can iterate over.

# Built in Object Methods - research