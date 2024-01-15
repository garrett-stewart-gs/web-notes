# Testing
The smallest section of code that we usually test is a FUNCTION. This is because it is difficult to run a test for an expression like 
```js
let x = 2;
```

It is also notoriously hard to test console.log statements, so the vast majority of functions will RETURN values instead of printing values.

Testing is ALWAYS actual vs expected. 

Usually, you do not want to have PRODUCTION CODE mixed with TESTING CODE!

## Manual Testing
Manual testing is very labourious and can result in the programmer missing 'holes' in their code. This is where automated testing comes in.

## What is Node?
It is a c++ application that interprets js so it can run on a computer. It is actually c++ wrapping the v8 engine (v8 engine is the chrome JS interpreter)

## Node Packages (Official + userCreated)
Using the function:
```js
const variable = require('/file/path') //for files you wrote
const variable2 = require('assert'); //for packages you've downloaded and installed with npm
```
You can import other packages.

## Exporting your Code as Modules
```js
module.exports = {}
```
you can overwrite this object, or you can add to the object. It is an object so you can use object syntax to specify how you want to do it.

```js
module.exports = functionName;
```
overwrites the object. You can also add an array of functions or objects. (arrays is not common)

```js (double check this Garrett)
module.exports = {
  functionNameKey : functionName,
  functionName2Key : functionName2
}
```
adds to the object.

## Test-Runners (Automated Testers)
Mocha is an example of a test-runner. Test Runners can run 100s of tests in a few seconds, which frees up the programmer's times.

## NPM (the amazon of node packages)
The NPM website has a database of packages that you can install with the npm tool for Node.

YOu can find packages that meet your use case on the website. Look for WEEKLY DOWNLOADS, anything above 100,000, because they have been 'battle-tested' They will not have enough errors.

If you want to see what code composes that package, you can usually check out the linked GITHUB. 

There is a compilation of published versions in the top right corner. Generally, install the newest version, UNLESS it is incompatible.

If you want to download it, there is an install command shown on the right hand side

### Before installing Packages, we need to CREATE  a package
```
npm init
```
Just use default values if you want. You can change it in a json file that is created once you finish the initialization process

Before release, you can set the version to 0.1.0, instead of 1.0.0. 1.0.0 is used when the packages is released

entrypoint = index.js ALWAYS. Index is known to be the most important file in the directory. In this file, we import all the project files.

### Once the package is created, you can run commands for npm to install packages
Running npm install [package] will change the json file to include "dependencies" and inside the dependencies will be a list of packages being used.

If you mess up installing something, you can delete Node_modules folder + package-lock.json. You can also delete dependencies in package.json. Then start again. This is useful if you forgot to use the correct tags (ie. npm install xxx --save-dev)

## Make sure not to upload NODE_MODULES FOLDER
Make .gitignore file, and populate it with teh files/folders you want it to ignore. 

### ALWAYS IGNORE CODE YOU DIDN'T WRITE

Each new line is a file/folder you are ignoring.