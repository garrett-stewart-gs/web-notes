Promises are OBJECTS.

methods like fs.readFileSync return a 'string' - QUESTION - IS THAT A PROMISE?

3 States:
  1. pending
  2. fulfilled
  3. rejected

new Promise(cb1,cb2); = constructs a new promise. takes 2 callbacks as parameters.
  cb1 = callback if fulfilled
  cb2 = callback if rejected

.then = only called if cb1 is called on promise execution. runs as soon a promise is fulfilled

.catch = only called if cb2 is called on promise execution. runs as soon as a promise is rejected.

Both .then & .catch will execute IMMEDIATELY if you use it on a promise that is NOT PENDING.


promise.race. - takes an array of promises. WHAT DOES IT ACTUALLY DO???

promise.all(). takes an array of promises, and executes if ALL PROMISES in that array are fulfilled.

