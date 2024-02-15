## EJS Templates with Express
### Procedure
1. npm install ejs (must be installed, but does not need to be imported)
2. create express server
3. create routes directory with router file(s)
4. import the router file(s) inside the main server file
5. set the view engine to EJS. ***NOTE You only set the view engine once, inside the main express application file (ie. server.js, index.js, or app.js) If you are using Router files with express.Router(), you do NOT set the view engine multiple times!*** 
6. create a 'views' directory at project root folder. If it is located at the project ROOT, you do not need to specify where the views folder is via app.set('views','./views');
7. use the desired routes (app.use('filepath',routeObject))
8. create router file(s) and ensure the router object is being exported with module.exports (so the main file can import it at step #4)


### Example Code
```JS
//Main Server File (app.js, server.js OR index.js)
const express = require('express');
const app = express(); //create express server
const port = 3000;
const userRoutes = require('./routes/users');

app.set('view engine', 'ejs'); // sets the view engine to EJS, so now the express app can 
app.set('views','./views'); // configure the view engine to look in the appropriate subfolder. This is OPTIONAL if the views folder is located inside the ROOT of the project.

app.use('/users',userRoutes);

app.listen(port,()=>{
  console.log(`app listening on port ${port}`);
});

//--------------------------------------------------------------------------
// router file (example name = users.js)
const express = require('express');
const router = express.Router();

router.get('/profile',(req,res)=>{ // GET /users/profile
  const templateVars = {
    id: 'id',
    username: 'username',
    email: 'email',
  }
  res.render('profile', templateVars); // render template from /views/profile.ejs and pass templateVars to it
});

module.exports = router;

```