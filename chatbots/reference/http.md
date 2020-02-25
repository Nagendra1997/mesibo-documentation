---
description: Mesibo Http Class 
keywords: chatbot, script, scripting
title: Http 
---

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

`Http.ondata` <sub>compulsory</sub>  
An event listener to be called when a response is received from the server.

The response to the request will be null if the request was unsuccessful. To check the error you can inspect the response status code using the property `Http.respCode`.

`Http.respType` <sub>Read Only</sub>  
Returns the type of the response, can be `json`, `blob`, `text`, `xml`, etc. 

`Http.respCode` <sub>Read Only</sub>  
Returns the status code of the response. For example, if your request is successful and the response is valid the response code is `200`.  

### Methods
`Http.send()`   
Make an HTTP request

`Http.abort()`  
Abort an ongoing request

## Usage notes

### Global instantiation restriction  
An object of the class `Http` cannot be instantiated in global context. Ensure that you are creating the instance inside a scoped area or function.

