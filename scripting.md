</ If about module />
## Mesibo Scripting Module

Mesibo On-Premise provides you greater control over your data. With the loadable module architecture you can add new features on top of mesibo to extend it's capabilities to build powerful chatbots, filters, translators, etc.
The module interface is provided in native C/C++ platform which ensures raw performance and stability.

Now we offer you a more powerful way of building with mesibo.
</This is about the scripting MODULE/>

## Introducing Mesibo Scripting 
Mesibo is built by developers for developers. We believe in making our platform open and developer friendly to help you realize your next big idea. Now we present to you Mesibo Scripting - An easy to use interface that allows you to use the core platform of Mesibo with the wildly popular Javscript.

You can write custom logic in Javascript and let the scripting engine take care of converting that logic into high performance Native code. This creates unlimited creative possibilities for building with Mesibo. You can build chatbots, filters, translators, implement socket and database logic all from the comfort and ease of Javascript along with the performance of native code!

For example, here is a glimpse of what you can do with Mesibo Scripting. This code snippet sends a custom reply to any message recieved. 
```javascript
Mesibo_onMessage(p, data, len){
	//swap p.from and p.to
	const message = "This is a custom response";
	mesibo_message(np, message, message.length);
}
```
Lets do something way more cooler. This message can be a query to your chatbot. You can even connect with a chatbot service of your choice. You can make a REST call your Chatbot API endpoint, get the response and send it back as a reply.
```javascript
Mesibo_HttpCallback(cbdatam , response, len){
	//extract response
	mesibo_message(p, rp, len_);
}

Mesibo_onMessage(p, data, len){
	//
	mesibo_http(, callback, )
	return MESIBO_RESULT_CONSUMED;	
}
```
Mesibo scripting enables you to achieve a tighter integration with your application and greater control over the core platform of Mesibo.

The scripting interface is provided through the Mesibo Cloud console and you need not configure or install anything additional to use it. Just login to your application console and get going!

### Examples for using Mesibo Scripting
