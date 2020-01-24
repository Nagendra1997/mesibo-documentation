## Message  
The `Message` class can be used to send messages. Messages received in the callback `mesibo.onmessage` will be ofthis type. 

### Constructor  
`Message()`  
The constructor initializes the Message class. It must be called before you perform any method calls. 

### Properties  

`Message.aid` <sub>compulsory</sub> 
Application ID. If you are sending a message, it is necessary to set this field to a vaild aid.

`Message.id` <sub>compulsory</sub> 
ID of the incoming message. If you are sending a message, it is necessary to set this field to a vaild 
`unsigned 32-bit` integer.

`Message.refid`  
 Reference id (id of another message) to which the current message can be linked against.

`Message.groupid`  
Group ID should be specified when sending a message to a group, 0 for one-to-one messages.

`Message.flags`  
Message Flags. If you need to get message status of sent messages set this field to the required flag value.
For example, to get deleivery status set this field to `Mesibo.FLAG_DELIVERYRECEIPT`.
Refer [status codes and flags](https://mesibo.com/documentation/api/real-time-api/data-structures/#messageparams)

`Message.type`  
Message Type, any arbitrary user-defined types

`Message.expiry`  
Message Expiry for an outgoing message (time to live), in seconds

`Message.toOnline`
Send the message only if the recipient is online
 
`Message.length`
To send raw bytes (byte-array), set the number of bytes.

### Methods
`Message.send()`   
Send a message

Example,

```javascript
function sendTextMessage(to, text) {
	
	//Create a new message instance
	var message = new Message();
       	
	//Unsigned 32-bit pseudo random number  
	message.id = Mesibo.random();
	
	//Destination user
	message.peer = to;
       	
	//Get status update on delivery and read of the message  
	message.flag = Mesibo.FLAG_READRECEIPT | Mesibo.FLAG_DELIVERYRECEIPT;
	
	message.data = text;

	//Send the Message!	
        message.send();	
}
```

`Message.sendBytes()`   
Send raw bytes. To send binary data, specify the number of bytes in `Message.length` and the raw bytes array in 
`Message.data`

## Usage notes

### Global instantiation restriction  
An object of the class `Message` cannot be instantiated in global context. Ensure that you are creating the instance inside a scoped area or function.

### Global Event listners
To receive the status of sent messages, ensure that you have initialized the global event listner `mesibo.onmessagestatus` and set the correct flag value in `Message.flags`.
