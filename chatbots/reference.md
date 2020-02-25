---
description: Mesibo Reference List of Classes 
keywords: chatbot, script, scripting
title: Reference 
---

This section includes the reference documentation for the various classes and utilities provided in mesibo scripting. 

## Core Functionality 

| Functionality Class  	| Description                                                     |
|:--------------------------------------------------------------------|:----------------------------------------------------------------|
| [Mesibo](/documentation/chatbot/reference/mesibo)                            | Defines the core mesibo functionality class and various callbacks like `onmessage`, `onmessagestatus`, `onlogin`, etc 
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
| [GoogleCloudService](/documentation/chatbot/reference/googlecloudauth)                            | A generic utility for various Google Cloud APIs like Dialogflow, Google Translate, Maps,etc requiring OAuth2 . 

## Miscellaneous Utilities 

| Utility | Description                                                                        |
|:-------------------------------------------------------|:-----------------------------------------------------------------------------------|
| [random32]()   | Returns a 32-bit unsigned integer 
| [random64]()   | Returns a 64-bit unsigned integer 
| [hash64]()  |Returns a hash value 
| [timeInMicros]() | Returns the time elapsed in microseconds since the Unix epoch 
| [timeInMillis]() | Returns the time elapsed in milliseconds since the Unix epoch      |
| [timeInSeconds]() | Returns the time elapsed in seconds since the Unix epoch         	|
