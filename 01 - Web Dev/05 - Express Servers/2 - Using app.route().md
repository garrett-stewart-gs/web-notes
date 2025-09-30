## app.route() Overview
This method allows you to chain route handlers for the same route path based on the request method (GET, POST, etc.).

Benefits:
- you only need to write the path ONCE
- all the code for that route is contained in 1 block of code 


Code Example:
```js
const express = require('express');
const usersRouter = express.Router();

router.route('/')
  .get(function(req, res) {
    // Handle GET request
    res.send('Get a list of users');
  })
  .post(function(req, res) {
    // Handle POST request
    res.send('Create a new user');
  });

module.exports = usersRouter;
```