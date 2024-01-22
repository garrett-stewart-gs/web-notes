# use connection function to setup a socket
Each socket object is a memory address, so each connection that is made makes a new socket with a different memory address

When you have a socket, that is like a communication lane between the server and the client. The client has 1 end and the server has 1 end. Communications/events between the server or client should be applied to that socket

Sockets have various methods, such as .write(), that allow you to send text data along the socket to and from the client/server.

```js
const connect = function () {
  const connectSocket = net.createConnection({
    host: 'localhost',// IP address here,
    port: '50541'// PORT number here,
  });
  connectSocket.on('connect', stream => {
    console.log('connection established');
    conn.write('Name: GDS');
  });

  // interpret incoming data as text
  connectSocket.setEncoding("utf8");
  connectSocket.on('data',(data) => console.log(data))
  return connectSocket; 
};
```

