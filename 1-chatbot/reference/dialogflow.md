---
description: Mesibo Dialogflow Class 
keywords: chatbot, script, scripting
title: Dialogflow
---
The `Dialogflow` class can be used to connect to a Dialogflow(chatbot) service endpoint over REST 

### Constructor  
`Dialogflow()`  
The constructor initializes the Dialogflow class. It must be called before you perform any method calls. 

### Properties  
To connect to a chatbot you can set the following properties for an `Dialogflow` class object. 

`Dialogflow.project_id` <sub>mandatory</sub>  
Google Cloud Project ID linked with your Dialogflow chatbot.

`Dialogflow.service_account` <sub>mandatory</sub>  
Google Project service account. A service account is identified by its email address, which is unique to the account.

`Dialogflow.private_key` <sub>mandatory</sub>  
Service Account Private Key. Follow the instructions [here]() to download your key as a JSON file.

`Dialogflow.message` <sub>mandatory</sub>  
Message text

`Dialogflow.oncomplete` <sub>mandatory</sub>  
An event listener to be called when a response is received from dialogflow endpoint.

`Dialogflow.result` 
The result value to the request will be nonzero if the request was unsuccessful. To check the error you can inspect this value. 

`Dialogflow.response` 
JSON Response received from Dialogflow.

### Methods
`Dialogflow.send()`   
Send message to Dialogflow


## Usage notes

### Global instantiation restriction  
An object of the class `Dialogflow` cannot be instantiated in global context. Ensure that you are creating the instance inside a scoped area or function.

## Example

```javascript
const dialogflow_project_id = "xxxx";
const dialogflow_service_account = "xxxx@xxx.iam.gserviceaccount.com";
const dialogflow_key = "xxxxx";

var d = new DialogFlow();
d.project_id = dialogflow_project_id;
d.service_account = dialogflow_service_account;
d.private_key = dialogflow_key;
d.message = p.message;
d.oncomplete = function (d){
	print(d.queryResult.fulfillmentMessages[0].text.text[0]);
}
 
d.send();

```
