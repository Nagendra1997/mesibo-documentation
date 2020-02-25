---
description: Mesibo Scripting Essentials
keywords: chatbot, script, scripting
title: Mesibo Scripting Essentials
---
{% include_relative nav.html selected="scripting" %}

In this section, we will look at some of the essential elements of mesibo scripting. Although you will be using the same Javascript you are already familiar with, there are a few mesibo specific extensions you need to use for handling incoming messages, sending responses, etc.

Let's get started with the global object `mesibo`.

## mesibo - the master object

There exists a global object, `mesibo` which is an instance of the class Mesibo(). It is accessible from anywhere in the script and used for configuration and setting various event handlers such as message handler, message status handler, login handler, timer handler, exception handler, etc.

Refer reference documentation to learn more - Mesibo class

## Receiving messages
You need to register an event handler to receive all the messages as per the script configuration.  You can register a message handler using `mesibo.onmessage` property:

```js
mesibo.onmessage = function(m){
	print("Received messsage from: " + m.from + " Content: " + m.message );
	return Message.CONSUMED;
}
```

The event handler gets invoked with a Message() object for every new message.  You can access all the the message properties such as message content, sender, time, etc. from the Message object.

> Note that you must mandatorily register `onmessage` handler. Mesibo will not execute the script with a missing handler as it serves no purpose.

### Deciding the fate of the message
The return value of `onmessage` handler decides whether the message was consumed by the script or to be passed to the destination. 

For example, when you are implementing chatbots, your script consumes the message. However, if your script is moderating messages, it consumes messages or passes messages to the actual destination depending on the app logic. You can indicate it by return value Message.CONSUMED or Message.PASS in `onmessage` handler. 

When you return Message.CONSUMED, the sender does not get any message or delivery status. If you wish to send message status to the sender, you can alternatively return Message.DELIVERED, Message.READ or Message.FAIL from onmessage handler as appropriate to your script logic. 

## Sending a message
To send a message, you only need to create a Message object, pass the data in the message property, and then send it.

```js
var m = new Message();
m.message = "This is a message";
m.send();
```

By default, Message() object is automatically populated from the incoming message.  The `from` field will be initialized the address of the user the script is running for. The `to` field will be the address of the user who has sent you the message. You can override the to and from values if requires.

```js
m.to = "to_user";
m.from = "from_user";
```

### Example of sending an automatic reply

```js
mesibo.onmessage = function(m){

	//Create a new Message object
	var reply = new Message();

	//Custom response text/data
	reply.message = "This is an automated response to your message: " + m.message;

	//Send reply!
	reply.send();
}
```

### Getting status of a sent message

To get the status of messages send from the script, you may register an event handler for `mesibo.onmessagestatus`. It is not mandatory to define this listener.

## Exceptions Handling
When an exception error occurs in your JavaScript, mesibo calls `onexception` event handler with exception string so that you know what has gone wrong and fix it. You can register the exception handler like below:

```js
mesibo.onexception = function(e){
    	var m = new Message();
	m.message = e;
	m.send();
 }
```

## Session
The session allows you to maintain context or store a value, for example, the user name for a chatbot conversation.  Whatever information you store in session will be stored in the database and will be available when a message arrives from the same user. You can store any arbitrary object in the session. For example,

```js
mesibo.session.name = "Arya Stark";
```

The `session` is unique for each sender-receiver combination per script, and it will be automatically saved and retrieved for each sender-receiver combination. You can store up to 8KB of data per session.

The `session_id` is a unique read-only identifier that identifies the session and unique for each sender-receiver combination per script.

You can access `session` and `session_id` from `mesibo` global object as well from all the mesibo specific class instances. Any changes made to the session properties will affect all references to the session object. For example, Mesibo.session, Message.session, Http.session, Socket.session, Dialogflow.session, etc.

For example,

```js
mesibo.session = {"session_key": "Session Value", "test": "OK"};
print(mesibo.session.test); //Prints "OK"

mesibo.session = "session string"; //Overriding session
print(mesibo.session); //Prints "session string"

var m = new Message();
m.session = "session message"; //Overriding session
print(mesibo.session); //Prints "session message"
```

### Advice on global variables
Unlike browser-based Javascript, which is single-threaded, mesibo javascript implementation is multi-threaded. Hence, we strongly recommend not to use global variables unless they are read-only.

To understand it better, imagine that a new message arrives while you are processing a previous message. In regular single-threaded Javascript, you will have to wait to finish processing the last message before handling the new message. This results in delayed and hence inadequate user experience. To better manage this situation, mesibo accomplished multi-threaded Javascript that allows you can process messages in parallel. However, concurrent access to global variables requires thread synchronization, which severely impacts performance, especially when mesibo is handling millions of messages.

However, if required, you can store globals in `mesibo.globals` variable, and they will be available throughout the script execution for a message.


