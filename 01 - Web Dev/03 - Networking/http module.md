### Example of using http module (basic server)
```js
const http = require("http");
const PORT = 8080;

// a function which handles requests and sends response
const requestHandler = function (request, response) {
  const responseText = `Requested Path: ${request.url}\nRequest Method: ${request.method}`;
  response.end(responseText); //response object is like a socket. ACTUALLY, IT CONTAINS A SOCKET. However, it is still true: You have to use it to send the response Text
};

const server = http.createServer(requestHandler);

server.listen(PORT, () => {
  console.log(`Server listening on: http://localhost:${PORT}`);
});
```