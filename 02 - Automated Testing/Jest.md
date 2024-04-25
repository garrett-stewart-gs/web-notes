## Jest

### Jest Documentation
https://jestjs.io/docs/getting-started

### File Extensions/Formats
Jest tests can use the following file formats:
* js
* jsx
* ts
* tsx

### Folder Structure
Jest searches the entire project directory for test files. It can only find test files that match the following paths:
1. src/components/__tests__/Button.js - You do not need to specify the individual test file is a test with the .test.js extension, IF it is located in the __tests__ folder (it is optional). 
2. src/components/Button.test.js - if you are not depositing your tests into a __tests__ folder, you NEED to specify the file type is .test.js OR .spec.js.
3. src/components/Button.spec.js - .spec.js is usually used for cypress, and .test.js is usually used for jest


### Getting Started
Create-React-App (and other bootstrapping tools) often include and install jest by default. However, you can manually install it if needed:
```shell
npm install --save-dev jest
```

Once jest is installed, link the command 'npm test' to run the jest suite by adding the following to the package.json file: 
```json
{
  "scripts": {
    "test": "jest"
  }
}
```

To begin testing, you need to create a test file and a file with code that needs testing. You will export the code that needs testing and import it into the test file. For example:
```js
//code that needs testing
const sum = (a,b) =>{
  return a + b;
};

//export the code that needs testing
export default sum;
```

```js
import sum from './sum.js'; //file path may change

//group tests for the same code block inside a describe function
// You will have multiple tests inside 1 describe function/block
describe('sum function tests',() => { // string argument displays on the jest console, so you can decipher which block of code you are testing

  //'test' and 'it' are synonymous, they are aliases for eachother.
  test('sum of 1 and 2 is 3',() => { // string argument displays on the jest console, so you can decipher which tests are passing/failing for a given block of code;
    expect(sum(1,2)).toBe(3);
  });

  //here is a second test for the 
  test('sum of 1 and 3 is 4', () => {
    expect(sum(1,3)).toBe(4);
  });
});
```

### Coverage
We can run the Jest tool and configure it to provide a coverage report. The command we use to run Jest is npm test which runs Jest in "watch" mode. We configure Jest by passing it a series of its useful options. We want to enable full coverage, which means that we need to disable the "watch" mode. The following command produces a coverage report.

```npm test -- --coverage --watchAll=false ```

1. "File" contains the project files labelled by file or directory name.
2. Code "statements" covered during testing.
3. Code "branches" covered during testing.
4. "Functions" called during testing.
5. "Lines" of code executed during testing.
6. Untested line numbers.
