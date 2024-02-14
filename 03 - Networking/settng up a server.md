//SERVER

const net = require('net');

const server = net.createServer()
const port = 4242; //between 3000-6000
server.listen(port,function(){
  console.log('server is listening on port', port);
});
server.on('connection', (socket) =>{
  console.log('someone joined the server');
})


//CLIENT
const net = require('net');
const config = {
  port: 4242, //must match server
  host: 'localhost' (IP address, if not you are not running the server & client on the same machine)
};

client.setEncoding('utf-8');
const client = net.createConnection(config);                                   