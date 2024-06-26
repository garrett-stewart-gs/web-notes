## Node Postgres
A library that allows node js (and any files running in it) to access postgres sql servers.

## Procedure
1. install node postgres with ```npm install pg```
2. import a connection class (Pool or Client)
3. create new connection object using the imported class (Pool or Client) and specifying it's configuration (host, port, database, username, password). ***The configuration should use environment variables before it is posted in a public space***
4. Create Query Files for modularizing your queries to the database

## Example Code (without environment variables = risky)
```js
const { Pool } = require('pg');

const config = { // establish database server connection details. You do not need to do this with a variable, you can just pass an anonymous object into a new Pool or new Client. Using a variable like this increase readability though.
  host: 'localhost',
  port: '5432' //this is the postgres server's default port
  database: 'lightbnb' // this is the name of the database you have already created, but you need to connect to
  username: 'labber' // postgres server username that you have already created & set by launching the postgres server
  password: 'labber' // postgres server password that you already set by launching the postgres server
}

const pool = new Pool(config); //establish connection immediately before query

//parameterized query - for escaping user input (sanitation)
pool.query(`INSERT QUERY HERE $1 $2 $3`, [user, fields, here]); // using second parameter (escape array) to insert USER-ENTERED data, allows node postgres to SANITIZE your inputs. This cures sql injection. USE ESCAPE ARRAY WHEN YOU DO NOT TRUST THE SOURCE

pool.query(`INSERT QUERY HERE ${internal} ${field} ${here}`); // you do not have to use an escape array if the template literal variables are generated internally. This is because you can trust the source (ie. yourself)

pool.end() // sever connection to postgres server when query is complete
```