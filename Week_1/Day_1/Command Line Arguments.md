# Command Line arguments

### Why Use Command Line Arguments?

The following are some of the major benefits of using command line arguments:

  * You can pass information to an application before it starts. This is particularly useful if you want to perform large number configuration settings.
  * Command line arguments are passed as strings to your program. String data types can easily be converted to other data types within an application, making the arguments very flexible.
  * You can pass an unlimited number of arguments via the command line.
  * Command line arguments are used in conjunction with scripts and batch files, which is particularly useful for automated testing.

### process.argv

This is an array used to retrieve arguments. More specifically, it is a global object that you can use without importing any additional libraries to use it. Here is an example of how to use it: 

```javascript
'use strict'; // what does this do?

for (let j = 0; j < process.argv.length; j++) { //process.argv is an array, which is why we can use the .length method
    console.log(j + ' -> ' + (process.argv[j]));
}```