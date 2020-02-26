---
description: Learn how to create your first chatbot  
keywords: chatbot, script, scripting
title: Creating your first chatbot 
---
{% include_relative nav.html selected="sample" %}

In this tutorial, we will create a simple chatbot with mesibo scripting. Mesibo scripting allows you to easily interface with chatbot and NLP engines like Dialogflow, IBM Watson, Microsoft Azure Bot Services, etc. For this sample, we will be using Dialogflow, but you can modify this example to connect with any other services.

### Prerequisites
- We will not get into the details of building the Dialogflow agent from scratch. Please refer to the [Dialogflow Docs](https://cloud.google.com/dialogflow/docs/quick/api) for learning the basics of Dialogflow and building your chat agents.
- DialogFlow Project ID and the keys which you can get from DialogFlow console.

## 1. Create a Dialogflow agent
Go to Dialogflow Console and create an agent. Refer to [Creating an Agent](https://cloud.google.com/dialogflow/docs/quick/api#create-an-agent). You can build your agent from scratch or import prebuilt-agents like Smalltalk.  If you wish to customize the responses, you can do so by clicking on the intent and modifying the Text response. Here is a detailed tutorial for [building a basic chatbot in Dialogflow]


### 2. Getting the credentials
To connect your chatbot with mesibo, you need to provide authorization credentials for your project.

 1. In the DialogFlow console, click on the gear icon to enter the settings page of your chatbot. In the Google Project section, click on Service Account. This will open the IAM Admin page for your project.

 2. You need to download the private key for this service account. To do this, Select the service account that is used by your chatbot and click on Create key (under Actions). When prompted, select Key Type as JSON and click create. This will download a JSON file. Example xxxxx-bf5310e9216a.json

 3. Open the JSON file and note the values for project_id, private_key, client_email. These parameters will be used for authorizing your API requests for your chatbot from your script to Dialogflow.

### 3. Getting hands-on with scripting 
In this sample chatbot, we will send every message we receive from the user to Dialogflow and send the response we receive from Dialogflow back to the user. We will be using the builtin Dialogflow class, which handles sending requests and getting responses from Dialogflow.

You need to send the following information to Dialogflow to get a valid response

 1. dialogflow_project_id: Use the project_id for the project you created in the setup steps.
 2. dialogflow_service_account: Use the client_email for the project you created in the setup steps.
 3. dialogflow_key: Use the private_key for the project you created in the setup steps.
 4. message: Message Query

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

That's it! 

Congratulations! In just a few lines of code, you made a commercially deployable chatbot!


