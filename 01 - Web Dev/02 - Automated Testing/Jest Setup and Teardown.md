## Jest Setup and Teardown
Often while writing tests you have some setup work that needs to happen before tests run, and you have some finishing work that needs to happen after tests run. Jest provides helper functions to handle this.

### Setup
* beforeEach()
* beforeAll()

### Teardown
* afterEach()
* afterAll

```js
// put setup/teardown + tests inside a describe block to control SCOPE. 
// setup/teardown located outside of a describe block applies globally to ALL TESTS. 
// setup/teardown located inside of a describe block applies ONLY to tests INSIDE the describe block
describe('all tests pertaining to specific code block', () =>{
  //this code runs ONCE before executing all of the tests
  beforeAll(()=>{
    initializeCityDatabase();
  });

  //this code runs ONCE after executing all of the tests
  afterAll(()=>{
    clearCityDatabase();
  });

  //this code runs before each test (1-3)
  beforeEach(()=>{
    initializeCityDatabase();
  });
  //this code runs after each test (1-3)
  afterEach(()=>{
    clearCityDatabase();
  });
  
  test('test 1', () => {
    expect().toBe();
  });

  test('test 2', () => {
    expect().toBe();
  });

  test('test 3', () => {
    expect().toBe();
  });
});
```