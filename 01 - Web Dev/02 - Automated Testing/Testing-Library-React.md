## React Testing Library (@testing-library/react)
The @testing-library family of packages helps you test UI components in a user-centric way

### Documentation
https://testing-library.com/docs/react-testing-library/intro

### Render
The render method is perhaps the most important method for testing react components!!
It returns the QUERIES from the DOM TESTING LIBRARY, 

Therefore, to get started with render, use destructuring to retrieve the desired query that is generated when you call the render method:
```js
// render returns all possible queries, but you destructure the ones you want to actually use in your tests
// You may need to pass data to the Component you are rendering, usually there is some form of props/data that needs to be passed
// There are 6 forms for each query: get, getAll, find, findAll, query, queryAll
const {getByPlaceholderText, findByTitle, queryByText} = render(<Component {...props} />)
const {getAllByRole, findAllByLabelText, queryAllByDisplayValue} = render(<Component {...props} />)
```

### Queries
Once you have rendered a Component and extracted the desired queries from the object returned by the render function, you can begin testing the rendered component. However, what queries are available for use?
***Here is a link to the full list of queries: https://testing-library.com/docs/queries/about/***
There are 6 forms for each query: 
1. Single Element Queries
  * get - returns the matching node. throws descriptive error if no elements found or multiple elements found
  * query - returns the matching node. returns null if no elements found. throws error if multiple elements found
  * find - returns a promise, which resolves when an element is found. The promise is rejected
    * When using "Find" queries, you should return the results of that query in the test, so that Jest can handle the promise
2. Multiple Element Queries
  * getAll - same as get, but returns an array of multiple matching nodes 
  * queryAll - same as query, but returns an array of multiple matching nodes 
  * findAll - same as find, but returns a promise that resolves to an array of elements.
    * When using "Find" queries, you should return the results of that query in the test, so that Jest can handle the promise



There are 8 "query-selectors" which are added to the tail of the queries above:
* ByRole
* ByLabelText
* ByPlaceholderText
* ByText
* ByAltText
* ByTitle
* ByTestId

For example, you chain the query-type with the query-selector like so: ```getByRole()``` or ```getAllByRole()```

**Note: the "query-selectors" must be chosen by what type of elements/values are available in the rendered component**

### Firing Events
You can use the react-testing-library to fire events to test your event handlers as well, using the fireEvent function:
```js
fireEvent(node:HTMLElement, event: Event);
```
Arguments:
1. HTML Element/Node - can be and element, but more commonly, it is a method/query that finds and returns an element/node
2. Event Object OR Event.Target Object - event properties can be configured as well

```js
//must import necessary functions from testing-library/react
import { render, screen, fireEvent, cleanup } from '@testing-library/react';

// Generic Syntax for fireEvent (offers flexiblility in configuring the event type)
// 2nd Argument = EVENT OBJECT
fireEvent(
  getByText("Save"), // 1st argument
  new KeyboardEvent('keydown', { // 2nd argument
    key: 'Enter',
    code: 'Enter',
    keyCode: 13,
    charCode: 0,
    shiftKey: false,
  })
);

// Specific Event Syntax for fireEvent (offers pre-configured options for event = less "hands-on")
// Second argument = EVENT.TARGET OBJECT
fireEvent.change(
  getByText("Save"), // 1st argument
  {target: {value: 'Something here'}} // 2nd argument. Note: target refers to HTML properties, doesn't have to be 'value', could be 'files' or 'name' etc..
);
```
