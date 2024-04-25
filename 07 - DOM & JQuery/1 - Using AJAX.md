## Procedure
1. Use $(document).ready(function(){}); to ensure all ajax requests are initiated when the DOM is fully-loaded
2. Specify the path/route you are making the AJAX request to using 'url'
3. Specify what type of request is being made:
  - GET
  - POST
  - PUT
  - DELETE
4. Encode data with .serialize(). If you are sending data in needs to be URLENCODED. This is because you are sending an HTTP REQUEST.
5. Specify what the request does on success and failure
6. AJAX has many other options you can specify...

## Example Code
```js
$.ajax({
  url: '/users',
  method: 'GET',
  success: (data)=>{ //get requests usually retrieve data, so you will need to grab that data
    displayUserData(data);
  },
  error: (error)=>{
    console.log(error);
  },
  //other ajax options go here. order does not matter
});

//post request for submitting login credentials
$.ajax({
  url: '/login',
  method: 'POST',
  data: $('.login-form').serialize(), //not necessary for GET (browse or read). serialize url-encodes the data because ajax is making an http request which requires url-encoding.
  success: () =>{ //post
    //do something based on the INTENT of the AJAX REQUEST
    renderMainPage(); //for example, when user logs in, render the main page
  },
  error: (error) =>{
    console.log(error);
  },
  // additional ajax parameters can go here as well. Order does not matter.
});

//post request with sending data via JSON
$.ajax({
  url: '/login',
  method: 'POST',
  contentType: 'application/json', //MANDATORY if you are sending json data
  data: JSON.stringify(data); //converts data into json string, which is also mandatory (NOTE: server must be using middleware: app.use(express.json());)
  success: ()=>{},
  error: (error)=>{
    console.log(error)
  },
});
```