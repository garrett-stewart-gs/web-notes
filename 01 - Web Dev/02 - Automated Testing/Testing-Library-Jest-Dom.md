## Jest-DOM (@testing-library/jest-dom)
This library provides a set of custom jest matchers that you can use to extend jest. These will make your tests more declarative, clear to read and to maintain. 

### Jest-DOM Documentation
https://github.com/testing-library/jest-dom
https://www.npmjs.com/package/@testing-library/jest-dom

### Getting Started
1. Create-React-App (and other bootstrapping tools) often include and install jest by default. However, you can manually install it if needed:
```shell
npm install --save-dev @testing-library/jest-dom
```

2. Import the package into the testing file (this will be in addition to other testing library imports):
```js
import '@testing-library/jest-dom';
```