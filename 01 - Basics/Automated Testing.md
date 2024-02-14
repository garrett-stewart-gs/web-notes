## Unit Testing
Unit Tests are typically applied to individual functions (small sections of code only).

Can be used as a safety net when implementing changes in your code (ie. refactoring). Unit tests can be used to let us know if we broke anything

Popular Tools for Unit Testing = Mocha, Jasmine, and Tape

## Integration Testing
Integration Tests are used to test how parts of the system work together. If unit testing is not enough, integration testing is often the next step. (ie. testing a database + an app).

Uses the same tools as Unit Testing

## Functional Testing
A.K.A. E2E Testing OR Browser Testing. Functional Testing is testing the COMPLETE functionality of some application. For Web Apps, you would need a tool to automate a browser. 

Where Unit & Integration Testing are used to validate RESULTS in a code, Functional testing should be used to validate the app as if it is a USER of the application.

You will not use a lot of functional tests on any given project, because they are very complex and difficult to maintain as a result. 

#### Functional tests should be used for testing COMMON USER INTERACTIONS


