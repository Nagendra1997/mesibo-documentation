---
description: Mesibo Google Services Class 
keywords: chatbot, script, scripting
title: GoogleCloudService 
---
The `GoogleCloudService` class can be used to connect to various Google Cloud API Services like Google Translate, Dialogflow, GoogleMaps, etc 

### Constructor  
`GoogleCloudService()`  
The constructor initializes the GoogleCloudService class. It must be called before you perform any method calls. 

### Properties  
To connect to a chatbot you can set the following properties for an `GoogleCloudService` class object. 

`GoogleCloudService.url`
REST URL endpoint of the API

`GoogleCloudService.project_id` <sub>mandatory</sub>  
Google Cloud Project ID linked with your GoogleCloudService chatbot.

`GoogleCloudService.service_account` <sub>mandatory</sub>  
Google Project service account. A service account is identified by its email address, which is unique to the account.

`GoogleCloudService.private_key` <sub>mandatory</sub>  
Service Account Private Key. Follow the instructions [here]() to download your key as a JSON file.

`GoogleCloudService.oncomplete` <sub>mandatory</sub>  
An event listener to be called when a response is received from the service endpoint.

`GoogleCloudService.result` 
The result value to the request will be nonzero if the request was unsuccessful. To check the error you can inspect this value. 

`GoogleCloudService.response` 
JSON Response received from the cloud service. 

### Methods
`GoogleCloudService.send()`   
Send data to the API endpoint.

## Usage notes

### Global instantiation restriction  
An object of the class `GoogleCloudService` cannot be instantiated in global context. Ensure that you are creating the instance inside a scoped area or function.

