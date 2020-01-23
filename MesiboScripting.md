# Mesibo Scripting

Mesibo Scripting offers you variety of functionality that help you interface with the core features of Mesibo over an easy to use Javascript interface.

## Mesibo  
The core class `Mesibo` defines a set of callbacks and utilities that you can use to send messages, get message flags, receive messages, etc.  

### Constructor
The class `Mesibo` is not instantiable. A single instance of the class `Mesibo`, named `mesibo` exists in global context whose properties can be initialized. Refer [Usage Notes]() for more details.

### Properties

`Mesibo.onmessage` <sub>compulsory</sub>  
An event listener to be called when message is receieved. It is a must to initialize this event handler.

For example, `mesibo.onmessage` can be initialized as follows:
```javascript
mesibo.onmessage = function(params, message){
	mesibo.log(message);
}
```
`Mesibo.onmessagestatus` <sub>optional</sub>  
An event listener to be called when status of the message is recieved. Note that the status for a message will be received only when you set the corresponding flag in the message parameters while sending the message. Initialization of this handler is optional. 

`Mesibo.onlogin` <sub>optional</sub>  
An event listener to be called when a user logs in or out of your application. Initialization of this handler is optional.

`Mesibo.MESIBO_RESULT_OK`  
Indicates Sucessful operation

`Mesibo.MESIBO_RESULT_FAIL`  
Indicates Failed Operation

For more [status codes and flags](https://mesibo.com/documentation/api/real-time-api/data-structures/#messageparams).

### Methods  
`Mesibo.sendmessage()`  
Utility to send a message 

`Mesibo.log()`  
Utility to print to console


## Usage notes

### Instantiation restriction
The class `Mesibo` is not instantiable. 

ie; The following operation is invalid  
```javscript
var m = new Mesibo()
```
Only a single global instance of `Mesibo` class , defined as `mesibo` exists throughout the lifetime of your script. 

### Global Initialization 

It is *necessary* to initialize the event handlers for the global `mesibo` object before you use any other class or function in your script. The handler `mesibo.onmessage` needs to be initialized compulsorily in your script. `mesibo.onlogin` and `mesibo.onmessagestatus` are optional handlers that can be defined if required.

## Http  
The `Http` class can be used to make REST calls to a request endpoint and receive data asynchronously.

### Constructor  
`Http()`  
The constructor initializes the Http class. It must be called before you perform any method calls. 

### Properties  
To make an HTTP call you can set the following properties for an `Http` class object. 

`Http.url` <sub>compulsory</sub> 
Both http and https URL are supported. You can also pass authentication information in URL. For example, https://username:password@yourapiurl.com

> Only connections to safe url endpoints are allowed. A list of urls considered safe is [available here]. If you wish to connect to an API endpoint which is on a domain not listed in our safe list, please file a support request [here]().
> Also note that there is no option to connect to a proxy or redirecting your request.

`Http.post` <sub>compulsory</sub>
Raw post data. Provide that post data as per the format required for your API request.

`Http.contentType`  
Content-Type header. You can request the resoponse to be of a particular type. For example, “application/json”.

`Http.headers`  
Any custom headers you like to send, such as contain Authorisation header, etc

`Http.userAgent`  
User Agent, default mesibo/x.x

`Http.referrer`  
HTTP referer header

`Http.origin`  
HTTP origin header

`Http.cookie`  
Send HTTP Cookie Header

`Http.encoding`  
HTTP content encoding header

`Http.cacheControl`  
HTTP cache control header

`Http.accept`

`Http.etag`

`Http.ondata`<sub>compulsory</sub>  
An event listener to be called when a response is received from the server.

The response to the request will be null if the request was unsuccessful. To check the error you can inspect the response status code using the property `Http.respCode`.

`Http.respType`<sub>Read Only</sub> 
Returns the type of the response, can be `json`, `blob`, `text`, `xml`, etc. 

`Http.respCode`<sub>Read Only</sub>  
Returns the status code of the response. For example, if your request is successful and the response is valid the response code is `200`.  

### Methods
`Http.send()`   
Make an HTTP request

`Http.abort()`  
Abort an ongoing request

## Usage notes

### Global instantiation restriction  
An object of the class `Http` cannot be instantiated in global context. Ensure that you are creating the instance inside a scoped area or function.

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

`Socket.ondata`  
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
