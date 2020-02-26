---
description: Mesibo Chatbot and Scripting - Introduction
keywords: chatbot, script, scripting
title: Mesibo Chatbot and Scripting - Introduction
---

# Message  
The Message class can be used to send messages. Messages received in the handler mesibo.onmessage will be of this type. 

# Constructor  

## Message()  
The constructor initializes the Message class. It must be called before you perform any method calls. 
Some of the property fields will be already initialized for you by mesibo and are also protected(read-only).

# Properties  

## Message.aid <sub>read-only</sub> 
Application ID. If you are sending a message, it is necessary to set this field to a vaild aid. Automatically initialized.

## Message.id 
ID of the incoming message. If you are sending a message, it is necessary to set this field to a vaild 
unsigned 32-bit integer. Automatically initialized.

## Message.refid  
 Reference id (id of another message) to which the current message can be linked against.

## Message.groupid  
Group ID should be specified when sending a message to a group, 0 for one-to-one messages.

## Message.flags  
Message Flags. If you need to get message status of sent messages set this field to the required flag value.
For example, to get deleivery status set this field to Message.DELIVERED.
Refer [status codes and flags](https://mesibo.com/documentation/api/real-time-api/data-structures/#messageparams)

## Message.type  
Message Type, any arbitrary user-defined types

## Message.expiry  
Message Expiry for an outgoing message (time to live), in seconds


# Methods

## Message.send()   
Send a message

### Syntax

javascript
Message.send()

### Parameters
None.

### Return Value
Mesibo.OK on success.
Mesibo.FAIL otherwise.

### Example

```javascript
function sendTextMessage(text) {
	
	//Create a new message instance
	var m = new Message();
       		
	m.data = text;

	//Send the Message!	
        message.send();	
}
```

## Static Properties
These properties are static proprties of the class Message and can be accessed without creating an instance of Message.

## Message.PASS
Pass the message data and parameters as it is to the recipient 

## Message.CONSUMED
Message is consumed by the script and will not be passed to the recipient 

## Message.FAIL
Message processing failed. 

## Message.READ
Flag value to get read status

## Message.DELIVERED 
Flag value to get delivery status

# Usage notes

## Global instantiation restriction  
An object of the class Message cannot be instantiated in global context. Ensure that you are creating the instance inside a scoped area or function.

## Global Event listners
To receive the status of sent messages, ensure that you have initialized the global event listner mesibo.onmessagestatus and set the correct flag value in Message.flags.
