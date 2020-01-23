#Mesibo Scripting
Mesibo Scripting offers you variety of functionality that help you interface with the core features of Mesibo over an easy to use Javascript interface.

## Mesibo
The core class `Mesibo` defines a set of callbacks and utilities that you can use to send messages, set message flags, receive messages, etc.  

Note that the class `Mesibo` is not instantiable. A single [global object] of the class `Mesibo` is defined as `mesibo` which exists throughout the lifetime of your script.

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

`Mesibo` also defines various [status codes and flags]() which you can use.

### Events 
The following event handlers can be initialized in your script, which can be fired for different events like: receiving a message,  receiving the status of a sent message, etc. 

`Mesibo.onmessage `*compulsory*`
Fired when message is receieved. It is a must to initialize this event handler.

`Mesibo.onmessagestatus `optional`
Fired when status of the message is recieved. Note that the status for a message will be received only when you set the corresponding flag in the message parameters while sending the message. Initialization of this handler is optional. 

`Mesibo.onlogin()` `optional`
Fired when a user logs in or out of your application. Initialization of this handler is optional.

### mesibo global object 
An object `mesibo` is defined as an instance in global context during initialization. The class `Mesibo` cannot be used to instantiate another object.

ie; The following operation is invalid
```javscript
var m = new Mesibo()
```
Only a single global instance of the `Mesibo` class exists throughout the lifetime of your script. 

It is *necessary* to initialize the callback functions for the global mesibo object before you use any other class or function in your script. The callback `mesibo.onmessage` needs to be initialized compulsorily in your script. `mesibo.onlogin` and `mesibo.onmessagestatus` are optional callbacks that you can define if required.

For example, `mesibo.onmessage` can be initialized as follows:
```javascript
mesibo.onmessage = function(params, message){
	mesibo.log(message);
}
```
## Http
The `Http` class can be used to make REST calls to a request endpoint and receive data asynchronously.

### Constructor
`Http()` The constructor initializes the Http class. It must be called before you perform any method calls. 

### Properties
To make an HTTP call you can set the following properties for an `Http` class object.

`Http.url`
Both http and https URL are supported. You can also pass authentication information in URL. For example, https://username:password@yourapiurl.com

>[!Note]
> Only connections to safe url endpoints are allowed. A list of urls considered safe is [available here]. If you wish to connect to an API endpoint which is on a domain not listed in our safe list, please file a support request [here]().
> Also note that there is no option to connect to a proxy or redirecting your request.

`Http.contentType`
Content-Type header. For example “application/json”.

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

### Methods
 `Http.send()` Make an HTTP request
 `Http.abort()` Abort an ongoing request

### Events
 `Http.ondata` 
Fired when an HTTP response is received.
The function parameter will contatin the response to the request or null if the request was unsuccessful.
