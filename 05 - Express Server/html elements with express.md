## HTML Attributes for a Form

```html
<form class="form-inline" action="/urls" method="POST">
  <div class="form-group mb-2">
    <label for="longURL">Enter a URL:</label>
    <input class="form-control" type="text" name="longURL" placeholder="http://"
      style="width: 300px; margin: 1em" />
    <button type="submit" class="btn btn-primary">Submit</button>
  </div>
</form>

```

Note the following attributes for some of the html elements:
  * action - set to the path: '/urls'
  * method - set to the value post. (NOTE: post sends data to request.body and get sends data to request.params)
  * name - set to databaseObj key named 'longURL'. You are essentially setting a VARIABLE NAME to be sent back to the server, but express will format it as an object

with method set to POST, and action set to '/urls', when a form is submitted, it will try to POST a change to the '/urls' path 