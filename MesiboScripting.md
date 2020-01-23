# Mesibo Scripting

Mesibo Scripting offers you variety of functionality that help you interface with the core features of Mesibo over an easy to use Javascript interface.

## Mesibo  
The core class `Mesibo` defines a set of callbacks and utilities that you can use to send messages, get message flags, receive messages, etc.  

### Constructor
The class `Mesibo` is not instantiable. A single instance of the class `Mesibo`, named `mesibo` exists in global context which you can use. Refer [Usage Notes]() for more details.

### Methods  
`Mesibo.sendmessage()`  
Utility to send a message 

`Mesibo.log()`  
Utility to print to console

### Error Codes  
Error codes which indicate the result of an operation.

`Mesibo.MESIBO_RESULT_OK`  
Sucessful operartion

`Mesibo.MESIBO_RESULT_FAIL`  
Operartion Failed

`Mesibo` also defines various [status codes and flags](https://mesibo.com/documentation/api/real-time-api/data-structures/#messageparams).

### Events  
The following event handlers can be initialized in your script, which can be fired for different events like: receiving a message,  receiving the status of a sent message, etc. 

`Mesibo.onmessage` <sub>compulsory</sub>  
Fired when message is receieved. It is a must to initialize this event handler.

For example, `mesibo.onmessage` can be initialized as follows:
```javascript
mesibo.onmessage = function(params, message){
	mesibo.log(message);
}
```

`Mesibo.onmessagestatus` <sub>optional</sub>  
Fired when status of the message is recieved. Note that the status for a message will be received only when you set the corresponding flag in the message parameters while sending the message. Initialization of this handler is optional. 

`Mesibo.onlogin` <sub>optional</sub>  
Fired when a user logs in or out of your application. Initialization of this handler is optional.

## Usage notes

### Instantiation restriction
The class `Mesibo` is not instantiable. 

ie; The following operation is invalid  
```javscript
var m = new Mesibo()
```
Only a single global instance of `Mesibo` class , defined as `mesibo` exists throughout the lifetime of your script. 

### Global Initialization 

It is *necessary* to initialize the event handlers for the global `mesibo` object before you use any other class or function in your script. The handler `mesibo.onmessage` needs to be initialized compulsorily in your script. `mesibo.onlogin` and `mesibo.onmessagestatus` are optional handlers that you can defined if required.

## Http  
The `Http` class can be used to make REST calls to a request endpoint and receive data asynchronously.

### Constructor  
`Http()` The constructor initializes the Http class. It must be called before you perform any method calls. 

### Properties  
To make an HTTP call you can set the following properties for an `Http` class object. Some properties like `Http.respType` and `Http.respCode` are set when you receive a response. 

`Http.url`  
Both http and https URL are supported. You can also pass authentication information in URL. For example, https://username:password@yourapiurl.com

> Only connections to safe url endpoints are allowed. A list of urls considered safe is [available here]. If you wish to connect to an API endpoint which is on a domain not listed in our safe list, please file a support request [here]().
> Also note that there is no option to connect to a proxy or redirecting your request.

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

`Http.respType`  
Returns the type of the response, can be `json`, `blob`, `text`, `xml`, etc. 

`Http.respCode`  
Returns the status code of the response. For example, if your request is successful and the response is valid the response code is `200`.  

### Methods
`Http.send()`   
Make an HTTP request
`Http.abort()`   
Abort an ongoing request

### Events
`Http.ondata`  
Fired when an HTTP response is received.
The function parameter will contatin the response to the request or null if the request was unsuccessful. To check the error you can inspect the response status code in the property `Http.respCode`.

## Usage notes

### Global instantiation restriction  
An object of the class `Http` cannot be instantiated in global context. Ensure that you are creating the instanceinside a scoped area or function.

 
