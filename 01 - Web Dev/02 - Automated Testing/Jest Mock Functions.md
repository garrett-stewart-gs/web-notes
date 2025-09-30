## Mock Functions in Jest
Mock functions can be used for the following:
1. Capturing the different calls made to the function and the arguments that were provided with each call
2. Returning any value we want for a specific test

```js
it("uses the mock implementation", () => {
 const fn = jest.fn((a, b) => 42);
 fn(1, 2);
 expect(fn).toHaveReturnedWith(42);
});
```