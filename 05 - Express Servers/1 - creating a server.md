## Starting a Server Procedure
1. Import express 
2. Establish server configuration
3. Start/Initiate server
4. 


## Example Code
```js
const express = require('express');

const app = express(); // creates express application. 
const PORT = 3000; //server config.

//MIDDLEWARE GOES HERE

//ROUTES GO HERE

app.listen(PORT, ()=>{
  console.log(`Server listening on port: ${PORT}`);
});

```

## Routing Functions



### app.route()