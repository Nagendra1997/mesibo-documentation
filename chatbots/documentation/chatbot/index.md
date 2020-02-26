---
description: Mesibo Chatbot and Scripting - Introduction
keywords: chatbot, script, scripting
title: Mesibo Chatbot and Scripting - Introduction
---
{% include_relative nav.html selected="intro" %}

Mesibo chatbot & scripting platform allows you to harness the power of Javascript to process each message your users are sending and receiving. You can create a JavaScript script that can carry out your own decision logic to process each message to create interesting chatbots and apps.

Below are two simple examples before we drive deeper into the mesibo chatbot and scripting platform.

For example, you can implement the following logic in javascript to respond with the current day, when a user queries "what day is it today?"

```js
mesibo.onmessage(m) {
	
	var response = process(m.message);

	send_response(response);
}
```

Another example would be the following scenario for getting weather status:

```js
mesibo.onmessage(m) {

	var eather_today = get_weather_prediction();
	send_response("The weather prediction for today is"+ weather_today);
	if("rainy" == weather_today)
		send_respnse("Don't forget to take your umbrella!");
}
```

Isn't it easy and powerful? This offers unlimited creative possibilities - you can build chatbots, filters, translators, implement socket, and database logic, all from the comfort and ease of Javascript. You can connect with any APIs of your choice such as NLP Engines for your chatbot backend, Weather prediction API, traffic or maps APIs, etc.
 
The best part is that mesibo chatbot and scripting platform is available at no additional cost; you can download the entire platform on your own servers and use it at no extra cost. Hence, there are no reasons for you to use any other platform that charges per conversation or charges per bot. In our opinion, such charges are simply exorbitant and unreasonable to developers.  Mesibo is built by developers for developers and we tried to address many of the limitations of the existing platforms, both technical and otherwise. 

mesibo scripting is built on V8 - [Google’s open-source, high-performance JavaScript and WebAssembly engine](https://v8.dev), written in C++, used by chrome and node.js, and offers blazing fast performance. It enables you to write chatbots in javascript, a familiar territory instead of learning new technology or fiddling with those XML and inadequate UIs some other platforms uses.

> Note that chatbot and scripting platform can not work with mesibo end-to-end encryption.

Now let's get started on the platform itself.

{% include github-file.md url="xxxgithub/mesibo/samples/raw/master/js/basic-demo/mesibo-demo.js" line="" %}

## Basic Concepts
You can specify your customised logic for each event such as when you receive a message, when a user logs in,etc and also use core utility functions provided by mesibo to perform different actions like sending a message, making an HTTP request, connecting to a socket, writing to a database, etc.

The scripting interface is provided through the Mesibo Cloud console and you need not configure or install anything additional to use it. Just login to your application console, load your script and get going! 

You can configure scripts that will be triggered when a user sends data or receives  data. You can also configure a default app script that runs for all events at the app level.

### How Mesibo Scripting Works
mesibo makes it extremely simple to make chatbots or your scripting apps.

1. Write a javascript with functions performing your custom logic for each event, such as when you receive a message, or message status, when a user logs in, etc.

2. Upload script in the mesibo console and specify when this script should be invoked. You can invoke the script you have uploaded for all the messages for all the users or a particular user. You can also specify the direction (from and to) of the message. 

3. That's it, mesibo will invoke your script as requested. Your script can analyze the message and decide on the action to perform, for example, respond, moderate, translate, modify, drop, or pass as it is.

You can use the same script for multiple users or different scripts for different users. You can even use different scripts for the same users in each message direction.

The scripting interface is provided through the Mesibo Cloud console, and you need not configure or install anything additional to use it. Just login to your mesibo console, load your script, and get going!

{% comment %}
## Pricing of the Scripting Platform

Mesibo offers a Pay-as-you-GO pricing model. If you are using Mesibo Scripting on the cloud, you will be charged for running your script on a per-second basis. ie; If your script is active for 10 seconds, then your charge will be $XX/second * 10 seconds = $YY. 

However, if you are using Mesibo Scripting on your own server by loading the [scripting module]() there are no charges whatsoever.

Please not that all other charges for using Mesibo APIs and services are independent and remain the same.
For more details refer [Mesibo Pricing](https://mesibo.com/pricing/) 
{% endcomment %}

## Conclusion of part one

In this part, we have learnt briefly about mesibo chatbot and scripting and what it can do. 

In part 2, we will learn more about how to upload and configure script on mesibo console

[On to Part 2 >>](uploading-scripts.md){: class="button outline-btn" style="margin-bottom: 30px; margin-right: 100%"}


