Closures are a javascript concept that is a core part of how React works and RENDERS pages. 

Closures are where a FUNCTION RETURNS A FUNCTION. Usually an 'inner-function' is declared and returned from inside an 'outer-function'. It is effectively the REVERSE of a CALLBACK FUNCTION. If a closure is created, the variables inside the outer-function and outside of the inner-function REMAIN ACTIVE.

Every time that you call the inner-function, it operates on the variables contained in the outer-function, and those changes are PERSISTENT. You can reset the changes that the inner-function made to the outer-functions variables by calling the outer-variable again!

Rendering is the process of displaying data by running a function from TOP TO BOTTOM. It is a function call! That means that it will only display information that is true/correct/up-to-date at the time it runs. The React Rendering function only occurs when the page is FIRST LOADED. ***Closures are necessary to maintain state when the page is re-rendered (ie. if you didn't have closures, updated values would be reset). This is accomplished with the 'USESTATE' function***

```js
//Here is an example of an enclosure for vanilla javascript
const outerFunction = () =>{
  let count = 0;

  const innerFunction = () =>{
    count++;
    console.log(count);
  }

  return innerFunction; 
}

let logIncrement = outerFunction(); //turns this variable into the 'innerFunction' (essentially a function declaration, because a function is the return value of 'outerFunction')

logIncrement(); //prints 1
logIncrement(); //prints 2
logIncrement(); //prints 3
logIncrement(); //prints 4

logIncrement = outerFunction(); //resets the STATE of the count variable (it is actually overwriting the old INSTANCE with a new/fresh INSTANCE)
logIncrement(); //prints 1
logIncrement(); //prints 2
logIncrement(); //prints 3
logIncrement(); //prints 4
```


```js
//Here is an example of a closure using the REACT library
import {useState} from 'react'; // IMPORTANT. if you use the useState() function for managing state, this must be imported, or you will get a reference error!

function App() {
  //always use const for declaring variable assigned a value from useState
  const [count, setCount] = useState(0); // The IMPORTED useState() function is passed a default value of 0, and it returns an array, where: 
    // the 0th element is the default value, and it is being named 'count'
    // 1st element is a FUNCTION that can update the value of the 0th element so that it does not get 'forgotten' or 'reset' when a re-render occurs
    //useState() employs a CLOSURE, where useState() is the OUTER-FUNCTION, and it returns the INNER-FUNCTION (we rename it 'setCount' here)

  const increment = function (){
    //count++; CANNOT WORK HERE, because count is a constant variable, and it is important that we declare it as a constant!!
    setCount(count + 1) // this is how we increment instead, by using the setCount() INNER-FUNCTION that useState() gave us. When setCount() is called, it triggers React to Re-render the page, and because the value of count exists within a closure, it's value is not forgotten or reset!!

    setCount(prev => prev + 1) // The INNER-FUNCTION returned from useState() can accept a function as an argument, which protects from 'Stale-State' bugs. This inner function has access to a variable we is ALWAYS named 'prev' which is guaranteed to be the most-up-to-date value of the variable that pertains to the INNER-FUNCTION.When you pass a callback-function to the INNER-FUNCTION, it knows to pass 'prev' as a parameter and how to operate on it, because the callback function's declaration includes this info.
  }

  return (
    <div className="App">
      <h2>React State</h2>
      <h4>{count}</h4>
      <button onClick={increment}>+</button> //creates a button that when clicked, it runs the 'increment' function. Note: you cannot use parenthesis, you need to pass it the function itself, not the results of a function.
    </div>
  )

}
```