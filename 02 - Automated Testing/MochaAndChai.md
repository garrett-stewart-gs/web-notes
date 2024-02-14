## Mocha
Mocha contains 2 critical functions called: 
```js
describe() // creates a header for your tests (optional, but necessary when testing multiple functions in the same original file)

it() //each 'it' is the test
```

Both functions take the parameter: 

* STRING as an argument/parameter. This is a readout for the person!

It takes an additional parameter:

* anonymous function - You do not need to name it. Inside the function, you can put all the tests you need to run. Could be 100s of tests. You will probably need to import functions like 'assert' before writing code for testing

## chai is an assertion library
chai has 3 different divisions that you can use to test code (should, expect, assert).

Chai's assert library has a TON of assertions that node does not have by default, so it gives you flexibility for testing.