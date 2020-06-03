---
description: Mesibo Reference List of Classes 
keywords: chatbot, script, scripting
title: Mesibo Javascript Class Reference 
---

Here you will find the reference documentation for all various mesibo javascript classes and utilities which you can use in mesibo scripting. 

## Core Functionality 

| Functionality Class  	| Description                                                     |
|:--------------------------------------------------------------------|:----------------------------------------------------------------|
| [Mesibo](/documentation/chatbot/reference/mesibo)                            | Defines the core mesibo functionality class and various event handler like onmessage, onmessagestatus, onlogin, etc 
| [Message](/documentation/chatbot/reference/message/)                              | The Message class that can be used to send messages

## Networking 

| Functionality Class | Description                                                                                                     |
|:--------------------------------------------------------------|:----------------------------------------------------------------------------------------------------------------|
| [Http](/documentation/chatbot/reference/http/)              | A networking utility class for sending REST API requests 
| [Socket](/documentation/chatbot/reference/socket/)                   | Connect to sockets(regular and websockets) to read and write data asynchronously


## Third Party APIs and Services 

| API                                                   | Description                                                                            |
|:------------------------------------------------------|:---------------------------------------------------------------------------------------|
| [Dialogflow](/documentation/chatbot/reference/dialogflow)                            | Connect to chatbots built on Dialogflow, send queries and receive responses(async) from your chatbots 
| [GoogleCloudService](/documentation/chatbot/reference/googlecloudservice)                            | A generic utility class that can be used for various Google Cloud APIs like Dialogflow, Google Translate, Maps,etc requiring OAuth2 . 

## Miscellaneous Utilities 

| Utility | Description                                                                        |
|:-------------------------------------------------------|:-----------------------------------------------------------------------------------|
| [random32](/documentation/chatbot/reference/utilities/#random32)   | Returns a 32-bit unsigned integer 
| [random64](/documentation/chatbot/reference/utilities/#random64)   | Returns a 64-bit unsigned integer 
| [hash64](/documentation/chatbot/reference/utilities/#hash64)  |Returns a hash value 
| [startTimer](/documentation/chatbot/reference/utilities/#starattimer)  |Start a timer for specified number of seconds and execute the event handler defined in mesibo.ontimeout. 
| [timeInMicros](/documentation/chatbot/reference/utilities/#timeinmicros) | Returns the time elapsed in microseconds since the Unix epoch 
| [timeInMillis](/documentation/chatbot/reference/utilities/#timeinmillis) | Returns the time elapsed in milliseconds since the Unix epoch      |
| [timeInSeconds](/documentation/chatbot/reference/utilities/#timeinseconds) | Returns the time elapsed in seconds since the Unix epoch         	|
