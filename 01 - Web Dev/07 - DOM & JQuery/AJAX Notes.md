## Asynchronous Javascript And XML (AJAX)

http requests with javascript use XHR object. XHR is old, so it can be messy.

Javascript can use FETCH instead of XHR, but we are not doing that right now:
1. create xml object
2. run .onreadystatechange method
3. open connection
4. send

JQuery can make this process a lot easier:

```js
$.ajax({
  url: 'http://www.example.com',
  method: 'GET',//js can genuinely do get post delete and put, where as http can only do get and post
  success: (response) => {},
  error: (err) =>{},
});
```

```js
//listening to form element
$form.on('submit', (event)=>{
  event.preventDefault(); //this is CRITICAL. You need to stop the webpage from making an http GET request!!
  $(this).serialize(); //ALSO CRITICAL. This encodes the data as a URL encoded STRING!! Note: if called on a form, you can just pass it the form. 

});

//once you have the url encoded data, you can call this function
$.ajax({
  url: '/url',
  method: 'POST',
  data: data,
  success: (response)=>{
    console.log(response);
    //can put function calls here to ensure sequence is maintained
    renderData(); //example function call to update
  }
})
```