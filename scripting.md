# Mesibo Scripting

Mesibo is built by developers for developers. We believe in making our platform open and developer friendly to help you realize your next big idea. In this step, We present to you Mesibo Scripting - An easy to use interface that allows you to use the core platform of Mesibo with the wildly popular Javscript.

Mesibo Scripting offers you variety of functionality that help you interface with the core features of Mesibo over an easy to use Javascript interface.

You can write custom logic in Javascript and let Mesibo Scripting take care of converting that logic into high performance native code. This creates unlimited creative possibilities for building with Mesibo. You can build chatbots, filters, translators, implement socket and database logic all from the comfort and ease of Javascript. 

Mesibo scripting enables you to achieve a tighter integration with your application and greater control over the core platform of Mesibo.

The scripting interface is provided through the Mesibo Cloud console and you need not configure or install anything additional to use it. Just login to your application console , load your script and get going! 

You can also use the scripting module on your own premise/servers if you are [hosting Mesibo](https://mesibo.com/documentation/on-premise/). You can load the scripting module and configure it to run your script.

### How to use Mesibo Scripting
You can specify your customised logic for each event such as when you receive a message, when a user logs in,etc and also use core utility functions provided by mesibo to perform different actions like sending a message, making an HTTP request, connecting to a socket, writing to a database, etc.

From Mesibo Console , you can configure to run a custom script for each user,etc. 

If you are using Mesibo On-Premise you can use the Scripting Module and configure the same.The module interface is provided in native C/C++ platform which ensures raw performance and stability.

### How Mesibo Scripting Works
The script you configure is executed at runtime through the Mesibo Native Interface. This is done by using a Javascript Engine which compiles Javscript code into Native C/C++ code and then evaluating that script on each call.
To do this, Mesibo uses V8 - Google’s open source high-performance JavaScript and WebAssembly engine, written in C++.

### Pricing of the Scripting Platform

Mesibo offers a Pay-as-you-GO pricing model. If you are using Mesibo Scripting on the cloud, you will be charged for running your script on a per-second basis. ie; If your script is active for 10 seconds, then your charge willbe $XX/second * 10 seconds = $YY. 

However, if you are using Mesibo Scripting on your own server by loading the [scripting module]() there are no charges whatsoever.

Please not that all other charges for using Mesibo APIs and services are independent and remain the same.
For more details refer [Mesibo Pricing](https://mesibo.com/pricing/) 


### Examples for using Mesibo Scripting

Here is a glimpse of what you can do with Mesibo Scripting. This code snippet sends a custom reply to any message recieved. 

### Sending an automatic response 
```javascript
mesibo.onmessage = function(message){
	//On receiving a message we send an automated reply to the sender 

	//Create a new Message object
	var replyMessage = new Message();

	//Copy params from the message received
	var replyMessage.params = Object.assign({}, message.params);

	//Switch sender and recipient 
	replyMessage.params.to = message.from;
	replyMessage.params.from = message.to;

	//Custom response text/data	
	replyMessage.data = "This is an automated response";
	replyMessage.length = replyMessage.data.length;

	//Message Sent!	
	replyMessage.send();
}
```
### Making an HTTP Request
Lets do something way more cooler. This message can be a query to your chatbot. You can even connect with a chatbot service of your choice. You can make a REST call your Chatbot API endpoint, get the response and send it back as a reply.

```javascript

mesibo.onmessage(message){
	//Send an HTTP request to a Chatbot API service like Dialogflow with a query containing message data 
	
	//Create an HTTP object
	var chatbot = new Http();
	
	//Set the request URL or endpoint
	chatbot.url = "https://dialogflow.googleapis.com/v2/projects/<PID>/agent/sessions/"+ <SID> 
		+ ":detectIntent";
	
	//Raw POST data containing the query message 
	chatbot.post = JSON.stringify({
		"queryInput": { 
			"text": {
				"text": message.data,  
				"languageCode" : "en"
			} 
		}
	});
	
	//Send auth token in header
	chatbot.extraHeader": "Authorization: Bearer XXXXXXXX";
	
	//Set request type to JSON
	chatbot.contentType": "application/json";
	
	//Store the query message for reference in the callback data object	
	chatbot.cbdata = message;
	
	//Define the event listener for receiving the response data from your chatbot
	chatbot.ondata = Mesibo_onChatbotResponse;	
	
	//Send the request!
	chatbot.send();

	return Mesibo.MESIBO_RESULT_OK;
}

Mesibo_onChatbotResponse(http){
	//We requested the response to be in JSON format		
	var rp = JSON.parse(http.response); 
	
	//This is the response format specific to Dialogflow
	var fulfillment = rp.queryResult.fulfillmentText;

	//Copy the the query message in callback data 
	//which we pass while sending the request 
	var queryMessage = http.cbdata;		
	chatbotResponse = Object.assign({}, queryMessage);
	
	//Sending response to the sender of the query
	chatbotResponse.peer = queryMessage.from;
	chatbotResponse.from = queryMessage.to;
	
	//Pass the fulfillment text in message data		
	chatbotResponse.data =  fulfillment;
	chatbotResponse.length = fulfillment.length;
	
	//Send the response!	
	chatbotResponse.send();
		
}
```
## Mesibo  
The core class `Mesibo` defines a set of callbacks and utilities that you can use to send messages, get message flags, receive messages, etc.  

### Constructor
The class `Mesibo` is not instantiable. A single instance of the class `Mesibo`, named `mesibo` exists in global context whose properties can be initialized. Refer [Usage Notes]() for more details.

### Properties

`Mesibo.onmessage` <sub>compulsory</sub>  
An event listener to be called when message is receieved. It is a must to initialize this event handler.

For example, `mesibo.onmessage` can be initialized as follows:
```javascript
mesibo.onmessage = function(message){
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

