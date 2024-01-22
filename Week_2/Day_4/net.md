## Net Module

Used for creating networks via the TCP protocol (or IPC). The net module DOES NOT support promises. This means you need to MAKE YOUR OWN CUSTOM PROMISES.

Remember - you need to track sockets on the server side (especially), but also the client side, because each socket has methods that are used to monitor/respond to events. Sockets are a channel for communications between the server and the client

Here is a list of IMPORTANT FUNCTIONS:

1. Creating a Server

    Purpose: Create a TCP or IPC server listening for connections.
    Server/Client: Server-side
    Inputs/Outputs: Accepts options and a connection listener; returns a net.Server instance.
    Promise-based Approach: Typically, you don't need a promise for creating a server, but you might use promises in handling client connections.
    Example:

    ```javascript

    const net = require('net');
    const server = net.createServer(socket => {
      socket.on('data', data => {
        // handle data
      });
    });

    server.listen(8080, () => {
      console.log('Server listening on port 8080');
    });```

2. Connecting to a Server

    Purpose: Create a client connection to a TCP server.
    Server/Client: Client-side
    Inputs/Outputs: Accepts connection options; returns a net.Socket instance.
    Promise-based Approach: You can wrap the connection logic in a promise.
    Example:

    ```javascript

    const net = require('net');
    const util = require('util');
    const connect = util.promisify(net.createConnection);

    async function connectToServer() {
      try {
        const client = await connect({ port: 8080 });
        client.write('Hello, server!');
        client.end();
      } catch (error) {
        console.error('Connection failed:', error);
      }
    }

    connectToServer();```

3. Reading Data from a Socket

    Purpose: Read data from the TCP or IPC connection.
    Server/Client: Both
    Inputs/Outputs: Socket provides data through events; no direct return value.
    Promise-based Approach: Handling socket data is typically event-driven, but you can use promises for specific control flow.
    Example:

    ```javascript

    // Assuming 'socket' is an existing net.Socket instance
    socket.on('data', data => {
      console.log('Received data:', data.toString());
    });```

4. Writing Data to a Socket

    Purpose: Send data through the TCP or IPC connection.
    Server/Client: Both
    Inputs/Outputs: Takes the data to send; write method returns a boolean indicating if the data was flushed to the kernel buffer.
    Promise-based Approach: You can create a promise-based wrapper for the write operation.
    Example:

    ```javascript

    const net = require('net');
    const server = net.createServer(socket => {
      socket.write('Hello from server!');
      socket.end();
    });

    server.listen(8080);```

