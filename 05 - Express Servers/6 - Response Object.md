## Response Object 

### res.send([body])
Sends a response of various types to the client. Depending on the type of the body, it sets the Content-Type header automatically (e.g., string, HTML, or JSON).

### res.render(view [, locals] [, callback])
Renders a view and sends the rendered HTML string to the client. Optional parameters include locals, an object whose properties define local variables for the view, and a callback for handling errors or manually sending the response.

### res.status(code)
Sets the HTTP status code for the response. It is a chainable alias of Node’s response.statusCode.

### res.redirect([status,] path)
Redirects the request to the URL derived from the specified path, with specified status code. If not specified, status code defaults to “302 Found”.

### res.sendFile(path [, options] [, fn])
Transfers the file at the given path. Sets the Content-Type response header field based on the file’s extension.

### res.json([body])
Sends a JSON response. This method is similar to res.send() but automatically sets the Content-Type header to application/json.

### res.set(field [, value])
Sets the response’s HTTP header field to value. It can also pass an object to set multiple fields at once.

### res.cookie(name, value [, options])
Sets a cookie name to value. The options object can be used to specify cookie attributes such as expires, secure, etc.

### res.clearCookie(name [, options])
Clears the cookie specified by name. The options object must match the options object used to set the cookie.

### res.download(path [, filename] [, options] [, fn])
Prompts a file to be downloaded by the browser. The filename parameter sets the name of the file in the download prompt. The optional callback fn is called on completion or error.