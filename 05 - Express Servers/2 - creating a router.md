## express.Router()
- Typically used for MODULARIZING your web application.
- Creates a router object, which is an 'isolated instance of middleware and routes' (a mini application, capable of performing middleware and routing function).

## Procedure
1. Create Express Application in separate file (ie. create a server). The server and router should exist in separate files. You can have multiple routers (ie. one for users, one feature X, one for feature Y, etc..)
2. Import the routes into the server file (this will correspond the the router files' module.exports)
3. Use the imported routes in the server file (this will define which routes belong to which router with app.use('/path', routerObject))
4. Create the mini-express application in a separate file (ie. express.Router())
5. 

## Example Router Code
```js
// Main server file (typically app.js OR index.js OR server.js)
const express = require('express');
const app = express();
const port = 3000; //can be any port. Optional: can setup port forwarding on your ISP Router for other people to connect
const usersRouter = require('./routes/users'); // imports the router that is being exported from the separate router file (ie. users.js)

app.use('/users', usersRouter); // uses the imported router file AND all routes inside that router file have a ROOT of /users

app.get('/',(req,res)=>{}) //routes can be used here, but bad practice if your goal is modularization

app.listen(port,()=>{
  console.log(`app listening on port ${port}`);
});

//----------------------------------------------------------------------------------------------------------------------

// router file (typically located in a routes subfolder AND named according to the route [ie. users.js for /users route])
const express = require('express');
const router = express.Router(); // creates router object

router.get('/', (req,res)=>{ //CRUD or BREAD
  res.send('example for /users') // note that you the get route '/' is implicitly '/users' because of the main server file's app.use('/users', usersRouter)
});
router.post('/', (req,res)=>{}); //CRUD or BREAD
router.put('/',(req,res)=>{}); //CRUD or BREAD
router.delete('/',(req,res)=>{}); //CRUD or BREAD

module.exports = router //exporting the router object, allows the main server file to use this router: app.use('/users', userRouter)
```