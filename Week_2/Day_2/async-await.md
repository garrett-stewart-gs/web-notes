# Async / Await

### Code:
```js
async function functionName1 () { //one way to declare an async func
  const response = await fetch("url"); //await can only be used inside an async function
    const jsonUserData = await response.json(); //doesn't run until response receives a value
    console.log(jsonUserData);
    return jsonUserData;
}

functionName1();  //starts function1, then moves on

const functionName2 = async () => { //another way to declare an async func
  const data = await functionName1();  //waits for function 1 to resolve
  console.log(data); 
}

functionName2();
```