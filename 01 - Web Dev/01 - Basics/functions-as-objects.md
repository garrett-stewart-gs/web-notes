# Functions as Objects
functions are objects in javascript, which means:
  1. Functions can be stored in variables and passed around
  2. functions can do everything that other objects do (ie. have properties)

### Callback Functions
Callback Function Qualities:
  1. it is passed by reference into another function. For example:
      ```javascript
      Array.forEach(() => {}) //inside forEach() is an ARROW FUNCTION/CALLBACK FUNCTION
      ```
  2. Higher Order Function = the function that receives the callback function as a parameter
  3. The higher order function (receiver) can call the callback function at anytime/frequency

  

