---
description: Learn how to create your first chatbot  
keywords: chatbot, script, scripting
title: Creating your first chatbot 
---

In this tutorial we will create a simple chatbot with mesibo scripting. The sample chatbot is built with Dialogflow.

## Mesibo Dialogflow Chatbot
With Mesibo scripting you can easily interface with chatbot and NLP engines like Dialogflow, IBM watson, Microsoft Azure Bot Services, etc. You can build powerful chatbots to craft novel messaging experiences all with the ease of Javascript with Mesibo Scripting.

Here, we show you an example of using Dialogflow to build a simple chatbot that can do some smalltalk. 

### Prerequisites
- We will not get into the details of building the Dialogflow agent from scratch. Please refer to the [Dialogflow Docs](https://cloud.google.com/dialogflow/docs/quick/api) for learning the basics of Dialogflow and building your chat agents.
- [Building a chatbot with Mesibo](https://mesibo.com/documentation/on-premise/loadable-modules/#code-references-and-examples) fro a quick review on building a simple chatbot with Mesibo Modules

## Building a smalltalk chatbot

### 1. Create a Dialogflow agent
Go to Dialogflow Console and create an agent with the project name `mesibo-dialogflow`. Refer [Creating an Agent](https://cloud.google.com/dialogflow/docs/quick/api#create-an-agent). From the section prebuilt-agents import the smalltalk bot. All the intents from t
the smalltalk bot will be imported into your project. If you wish to customize the responses, you can do so by clicking on the intent and modifying the Text response. Here is a detailed tutorial for [building a basic chatbot in Dialogflow]()

### 2. Getting the credentials
To connect your chatbot with mesibo, you need to provide authorization credentials for your project. 
1. Click on the gear icon to enter the settings page of your chatbot.  In the Google Project section click on `Service Account`. This will open the IAM Admin page for your project.
2. You need to download the private key for this service account. To do this, Select the service account that is used by your chatbot and click on `Create key` (under `Actions`). When prompted, select `Key Type` as JSON and click create. This will download a JSON file. Example `xxxxx-bf5310e9216a.json`
3. Open the JSON file and note the values for `project_id`, `private_key`, `client_email`. These parameters will be used for authoriing your API requests for your chatbot from your script to Dialogflow.


### 3. Getting hands-on with scripting 
Each message from the user contains the question. Do deliver an appropriate response, we need to find the [`intent`](https://cloud.google.com/dialogflow/docs/quick/api#detect_intent) behind that question. Based on the training data, the query is analysed to match a set of intents. We select the appropriate response to the intent that matched with the highest `detectionConfidence` and send the response linked with that intent.
### Sending a request to Dialogflow
You need to send the following information to Dialogflow to get a valid response

- `dialogflow_project_id`: Use the `project_id` for the project you created in the setup steps. 
- `dialogflow_service_account`: Use the `client_email` for the project you created in the setup steps. 
- `dialogflow_key`: Use the `private_key` for the project you created in the setup steps. 
- `message`: Message Query 

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
	var response = d.responseText;
	var rp = new Message();
	rp.message = response;
	rp.send();
}
 
d.send();
```

For sending the request to the dialogflow chatbot we send the message-id to identify the [session](https://cloud.google.com/dialogflow/docs/quick/api#sessions) and in the response that we send back to the user , the `refid` will have the message-id of the query message. This way the client who sent the query can know which message query is the given response linked to.


