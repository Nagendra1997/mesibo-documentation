## Socket 
The `Socket` class can be used to connect to a host through a scoket, to send and receive data asynchronously.

### Constructor  
`Socket(url)`  
The constructor initializes the  Socket class. It must be called before you perform any method calls.
Call the constructor by passing the url(For websockets) or host address(for regular sockets).

## Example
```javascript
//Regular Sockets
var s = new Socket("sock://example.com:80");

//Websockets
var ws = new Socket("wss://echo.websocket.org");
```

### Properties  
To connect to a socket set the following properties for an `Socket` class object.

`keepalive`  
Enable for persistent connection

`verify_host`

`enableSsl`  
Configure SSL 

`Socket.onconnect`  
An event listener to be called when connected to the server(host) 

`Socket.ondata`  <sub>compulsory</sub>  
An event listener to be called when data is received from the server(host) 

`Socket.onwrite`  
An event listener to be called when data is written to socket

`Socket.onclose`  
An event listener to be called when the connection is closed.

### Methods

`Socket.open()`  
Open a new connection on specified host.

`Socket.send()`  
Send data to connected host 

`Socket.close()`  
Close connection

### Example
```javascript
var s = new Socket("sock://example.com:80");
s.onconnect = function(s) {
	print("connected");
	s.send("GET / HTTP/1.0\r\nHost: example.com\r\nConnection: close\r\n\r\n");
}

s.ondata = function(s) {
	print(s);
}

s.open();
```

## Usage notes

### Global instantiation restriction  
An object of the class `Socket` cannot be instantiated in global context. Ensure that you are creating the instanceinside a scoped area or function.
