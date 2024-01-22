### HTTP
* hyperText Transfer Protocol
* requires Request/Response Cycle - One person is speaking at a time! (cannot send anything that is not requested for)


### Requests
* We have Get and Post methods (for thsi week)
  * GET - request to retrieve something
  * POST = I'd like to send info to the server
* Body - whatever data/content that gets sent (get generally does not have a body)

### Responses
* Status Code - every response HAS to have a status code
  * 1xx series = Low-level routing
  * 2xx series = everything is ok
  * 3xx series = redirection
  * 4xx series = client has made a mistake
  * 5xx series = there's something wrong with the server
* body 


### EXAMPLES
```JS
//TCP SERVER - from last week
const net = require('net');
const server = net.createServer();
const port = 4000;

server.on('connection', function(){})

server.listen(port);


```



```JS
//HTTP SERVER - for this week. **NOTE HOW SIMILAR THE SYNTAX IS
const http = require('http');
const server = http.createServer();
const port = 4000;

server.on('request', function(request,response){**code**}){ //this needs to actually send something back for the browser to stop waiting for the response

socket.write('this is my server response'); //this would be socket.write() does not send the contents immediately like it would with the http library. 

response.end('sending message to client now')//With http, we only have 1 chance to send a response, so we use the .end() method, and it actually send the message. This message INCLUDES EVERYTHING WRITTEN TO THE "RESPONSE" OBJECT + the contents of the .end() method.
}

server.listen(port);
```

favicon (favorite icon) - icons that are placed on tabs + bookmarks + app icons


```js
//EXPRESS - needs to be installed with npm install

const express = require('express'); //need to install with npm
const morgan = require('morgan'); //need to install with npm
const app = express();
const port = 4000;

//MIDDLEWARE - **needs to be placed at top of file, because all requests start from the top-down.
app.use((request,response,next)=>{ //next is a callback function that calls the next middleware in line or to the route handler when it is done.
  console.log('inside the custom middleware');  
});

app.use(morgan('dev')); //dev is an option that details only what we as devs want to see. There are other settings for other use-cases (see details on npm docs)

// GET /home
app.get('/home', () => { //this code executes for EVERY REQUEST INVOLVING THE /HOME PATH
  console.log('someone visited the home page');
  response.send('this is the home page'); //this is the combined version of .write() and .end() in the http module
});


// GET /about
app.get('/about',() => { //these callback function arguments in app.get() methods are ROUTE HANDLERS (they handle requests for specific routes)
  console.log('someone visited the about page');
  response.send('this is the about page');
});



app.listen(port,function(){}); //andy likes to put asynchronous stuff like this at the bottom of the page, but YOU CAN PUT IT ANYWHERE


```


#### NOTE: Express allows us to setup different 'functions' or Code blocks for each path. Without Express, you need to put ALL REQUESTS IN 1 CODE BLOCK/CALLBACK FUNCTION (.on('request'))


### Middleware
* Code that sits between the request and the response
* almost always a function
* usually parsing data from one format to another
* Most middleware is not written by you. You can get packages/off-the-shelf software
  * Example = npm morgan - Morgan is a request logger (logs every request for the server, so server activity becomes transparent)
  * NOTE: you should install morgan on EVERY express server you make





