## Socket 
The `Socket` class can be used to connect to a host through a scoket, to send and receive data asynchronously.

### Constructor  
`Socket()`  
The constructor initializes the  Socket class. It must be called before you perform any method calls.

### Properties  
To connect to a socket set the following properties for an `Socket` class object.

`Socket.host`  
Host name: url or ip address

`Socket.port`  
`unsigned 32-bit` Port Number

`keepalive`  
Enable for persistent connection

`enableSsl`  
Configure SSL 

`Socket.ondata`  <sub>compulsory</sub>  
An event listener to be called when data is received from the server(host) 

`Socket.onwrite`  
An event listener to be called when data is written to socket

`Socket.onclose`  
An event listener to be called when the connection is closed.

### Methods

`Socket.connect()`  
Open a new connection on specified host and port.

`Socket.write()`  
Send data to connected host 

`Socket.close()`  
Close connection


## Usage notes

### Global instantiation restriction  
An object of the class `Socket` cannot be instantiated in global context. Ensure that you are creating the instanceinside a scoped area or function.
