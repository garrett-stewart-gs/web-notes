## Jest Matchers
Complete list of Matchers here: https://jestjs.io/docs/expect

### Common Matchers
* expect() - returns an 'expectation object', which can be chained with other matchers:
  * toBe()
  * not.toBe - you can chain 'not' with any matcher to invert the result

### Truthiness Matchers
* toBeNull()
* toBeDefined()
* toBeTruthy()
* toBeFalsey()

### Numbers Matchers
* toBeGreaterThan()
* toBeGreaterThanOrEqual()
* toBeLessThan()
* toBeLessThanOrEqual()
* toBeCloseTo() - useful when rounding errors cause expect().toBeEqual() to produce a failed test

### String Matchers
* toMatch()

### Arrays/Iterable Matchers
* toContain()

### Exceptions Matchers (forced errors)
* toThrow() - useful if you need to test error handling

## Jest Modifiers

* .not - inverts the test results
* .resolves - unwraps the value of a fulfilled promise (ie. ```expect(Promise.resolve('lemon')).resolves.toBe('lemon')```)
* .rejects - unwraps the reason of a rejected promise (ie. ```expect(Promise.reject(new Error('octopus'))).rejects.toThrow('octopus')```)