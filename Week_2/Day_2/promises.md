# Async Principles

## Promises
Promises have 3 states:
* Pending
* Fulfilled
* Rejected

### Code

```js
const myPromise = new Promise((resolve,reject) =>{
  const error = false;
  if(!error) {
    resolve("Yes! resolved the promise!");
  } else {
    reject("No! rejected the promise.");
  }
});
```
