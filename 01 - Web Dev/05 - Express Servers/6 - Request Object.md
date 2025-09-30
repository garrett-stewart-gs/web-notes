## Request Object
### req.params
An object containing properties mapped to the named route “parameters”. For example, if you have the route /user/:name, then the "name" property is available as req.params.name.

### req.body
When using body-parsing middleware like body-parser, or express.json(), this property contains the parsed body of the request. This is useful for POST and PUT requests where the client sends data in the request body.

### req.ip
Contains the remote IP address of the request. If the app is running behind a proxy, you might need to use req.ips or the trust proxy setting to get the correct IP address.

### req.method
This property contains the HTTP method used in the request, such as GET, POST, PUT, DELETE, etc.

### req.headers 
An object containing the request headers. For example, req.headers['content-type'] gives the content type of the request.

### req.path
Contains the path part of the request URL.

### req.query
This property contains an object of a parsed query string of the URL. If your request URL is /search?keyword=Node, you can access it by req.query.keyword.

### req.url or req.originalUrl
Returns the request URL string. originalUrl preserves the original request URL, allowing you to reuse the same route definitions across many routes, whereas req.url may be rewritten for internal routing purposes.

### req.cookies
When using the cookie-parser middleware, this property contains parsed cookies attached to the client request. For example, req.cookies.name would retrieve the cookie named "name".

### req.accepts(types)
A method that checks if the specified content types are acceptable, based on the request's Accept HTTP header. It returns the best match, or if none of the specified content types is acceptable, it returns false.