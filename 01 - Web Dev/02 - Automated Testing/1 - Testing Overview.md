## Types of Testing
1. Static - ESlint, Prettier, Typescript. This type of testing costs very little and is very fast and simple. Typescript can lockdown what data types can be used, which limits what errors can occur.
2. Unit - Mocha/Chai, Jest. This type of testing is still simple and fast. It is more complicated and capable than static testing. This is where you are testing a small and isolated piece of code, usually a function.
3. Integration - Jest, Cypress. This type of testing is very similar to unit testing, but instead, you are testing that 2 pieces of code work together (ie. are the 2+ pieces of code integrated? You test for that)
4. End-to-End - Cypress. This type of testing is costly and complicated, but offers the most capabilities. You simulate the entire user experience. This type of test provides the highest amount of confidence.

## Word of Caution - Write Maintainable Tests
Do your best to write tests that don't need to change if you change the code. This is more difficult with end-to-end testing. Just be cautious about this, because when you make changes to your code and your tests no longer test the correct qualities, you double your work. Note: this is different from having your tests fail.

