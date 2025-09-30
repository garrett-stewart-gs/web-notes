## Environment Variables for PSQL Overview
It is very important that you do not share secret informtion online. It is easy to accidentally do this when pushing updates to github.

As a result, we need to use environment variables to store the neccessary info for connecting to the psql database. 

## Procedure
1. Install Node Postgres in your project with ```npm install pg```
2. Install dotenv in your project with ```npm install dotenv```. This package integrates environment variables from a '.env' file into the project, so the web server can retrieve the variables inside the '.env' file.
2. Determine the following information:
  ```
  DB_HOST=localhost
  DB_USER=labber
  DB_PASS=labber
  DB_NAME=example_db
  DB_PORT=5432
  ``` 
***Note: 5432 default port for psql***
3. Create a .env file and populate it with the information in step 1
  - Note: creating a .env file allows it's contents to be accessible to any program/app/script running in that environment
  - ***.env files MUST BE IGNORED in your .gitignore file. THIS IS ABSOLUTELY 1000% CRITICAL***
4. (OPTIONAL) Create a '.env_template' with blank values that is NOT IGNORED. This is useful if other people will be working on the project. They can copy the file and name the copy '.env' and then change the values how they see fit
5. Create a file (connection.js is a good name) that establishes the connection to the database using the environment variables and exports. Any files that query the database need to import this connection.js file (usually in your router files)
6. import dotenv into the main web server file at the VERY TOP. dotenv needs to be loaded in as early as possible

## Example Code
#### Main Server File Code
```js
require('dotenv').config(); //needs to be declared ASAP = top of server file
const express = require('express');
const app = express();
const PORT = process.env.PORT || 8080; // Sets port to value specified in .env file. If no value specified, defaults to 8080

//import router files here

//mount router files in app.use() here

app.listen(PORT,()=>{
  console.log(`app listening on port ${PORT}`);
});
```

#### .env File Code
```bash
DB_HOST=localhost
DB_USER=labber
DB_PASS=labber
DB_NAME=example_db
DB_PORT=5432
PORT=3000 #if this value is set, server will set the port to 3000 instead of 8080 in the server file code above
```

#### connection.js File Code
```js
const { Pool } = require('pg'); //import connection class 'Pool' from node postgres library. This is how node communicates with the psql server.

//create new pool connection object with parameters from private .env file
const dbPool = new Pool({
  host: process.env.DB_HOST,
  user: process.env.DB_USER,
  password: process.env.DB_PASS,
  database: process.env.DB_NAME,
  port: process.env.DB_PORT,
});

//do I need to use dbPool.connect() here? I don't think so, need verification

module.exports = dbPool;
```
#### Router Files Code
```js
//import connection object in ANY FILE THAT IS QUERYING THE DATABASE
const dbPool = require('./connection'); //path may vary. CAN ALSO IMPORT MODULARIZED QUERY FILES INSTEAD - WHICH MEANS IMPORTING dbPool INSIDE THE QUERY FILES INSTEAD
const router = express.Router();

//routes go here

module.exports = router;
```
