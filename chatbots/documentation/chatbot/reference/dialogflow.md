---
description: Mesibo Dialogflow Class 
keywords: chatbot, script, scripting
title: Dialogflow
---
The Dialogflow class can be used to connect to the Dialogflow chatbot service endpoint over REST 

### Constructor  

### Dialogflow()  
The constructor initializes the Dialogflow class. It must be called before you perform any method calls. 

### Properties  
To connect to a chatbot you can set the following properties for an Dialogflow class object. 

### Dialogflow.project_id 
Google Cloud Project ID linked with your Dialogflow chatbot.

### Dialogflow.service_account 
Google Project service account. A service account is identified by its email address, which is unique to the account.

### Dialogflow.private_key 
Service Account Private Key. 

### Dialogflow.message 
Message text

### Dialogflow.oncomplete 
An event listener to be called when a response is received from dialogflow endpoint.

### Dialogflow.result 
The result value to the request will be nonzero if the request was unsuccessful. To check the error you can inspect this value. 

### Dialogflow.response 
JSON Response received from Dialogflow.

### Methods

### Dialogflow.send()   
Send message to Dialogflow

## Example

```js 
const dialogflow_project_id = "xxxx";
const dialogflow_service_account = "xxxx@xxx.iam.gserviceaccount.com";
const dialogflow_key = "xxxxx";

mesibo.onmessage = function(m) {
	
	var d = new DialogFlow();
	d.project_id = dialogflow_project_id;
	d.service_account = dialogflow_service_account;
	d.private_key = dialogflow_key;
	d.message = m.message;
	
	d.oncomplete = function (d){
		var response = d.responseText;
		var r = new Message();
		r.message = response;
		r.send();
	}
	
	d.send();

	return Message.DELIVERED;
}
```

## Usage notes

### Global instantiation restriction  
An object of the class `Dialogflow` cannot be instantiated in global context. Ensure that you are creating the instance inside a scoped area or function.

