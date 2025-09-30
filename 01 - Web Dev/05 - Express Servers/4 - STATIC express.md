## Overview
express.static() is MIDDLEWARE. Can use it to serve images, css files, javascript files from a user-defined directory. 

## Procedure
1. create server
2. use express.static('public') as middlewhere
3. create 'public' directory to contain all of the static files that are being served up to the client. ***Note you can name the directory anything you want, but you need to tell express.static('directory_name')***

## Example Code
```js
const express = require('express');
const app = express();// creates express server
const port = 3000;

app.use(express.static('public')); //tells express to serve static files to client, where the static files exist in the 'public' directory

const otherRoutes = require('./routes/otherRoutes');// import other routes here. 
app.use('/otherRoutes', otherRoutes); //use other routes here

app.listen(port,()=>{ //starts server on specified port
  console.log(`app listening on port ${port}`);
});

```