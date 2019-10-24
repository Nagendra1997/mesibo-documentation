---
description: Mesibo Loadable Modules
keywords: open, source, on-premise, messaging, chat, voice, video
title: Mesibo Loadable Modules
---

### Sample Chatbot Module
It is extremely simple to get started with Mesibo to build chatbots, which can integrate powerful analytical abilities in speech, image recognition, Natural Language processing, etc in your backend using loadable modules. You can interface with any tool or library of your choice such as Dialogflow, IBM Watson, Tensorflow, etc using REST endpoints.

![Module Chatbot Sample](module_chat_bot.jpg)

Let's look at how you can build a chatbot using Mesibo Module:
- When you receive a message, you get the message text via the callback function `on_message` with the message data and it's associated message parameters as arguments. This message is the `query` to your chatbot.
- Now, you can create a chatbot module function that takes this message data/ text as input and sends it to a REST endpoint as HTTP Post data which contains the query using the function `http`. 
- In the function [http](),You pass the REST endpoint `url`, `post` data -which is as per the query format of your REST endpoint , callback function (where you will receive the response to your HTTP request) and Call back data (which is passed along to your callback function), `options` which will contain additional headers such as Authorisation header,etc (If required)
- Now based on your request, your chatbot model will process the query and send the appropriate response.
- In your callback function, you will receive the HTTP response for your query in the form of a JSON string. Send the  response using  `send_message` function.

### 1. Create a C/C++ Source file
First let us choose a name for our module. Since we will be building a chatbot , let our module name be `chatbot`. We  will create a C/C++ Source file with the same name as that of the module. ie; `chatbot.cpp`. Copy the header file `module.h` into your working directory and include it in your code.
```cpp
#include "module.h"
```
### 2.Configure file for the chatbot module
In this example we will configure a dialogflow chatbot. While loading your module you need to provide your configuration details in the file `/etc/mesibo/mesibo.conf` in the following format:
```
module = <module name> {
  <attribute name> = <attribute value>
  <attribute name> = <attribute value>
  .
  .
  .
}
```
For a (dialogflow)chatbot module the required configuration is as follows (Note the use of No space ):
```
module=chatbot{
project=<project name>
session=<session id>
base_url=https://dialogflow.googleapis.com/v2beta1
service_account_key=<access token> 
}
```
### Configuration paramters:
- `project` is the Google Project name that contains your dialogflow chatbot. 
- `session` The name of the session this query is sent to. Format: projects/<Project ID>/agent/sessions/<Session ID>, or projects/<Project ID>/agent/environments/<Environment ID>/users/<User ID>/sessions/<Session ID>. If Environment ID is not specified, we assume default 'draft' environment. If User ID is not specified, we are using "-". It's up to the API caller to choose an appropriate Session ID and User Id. They can be a random number or some type of user and session identifiers (preferably hashed). The length of the Session ID and User ID must not exceed 36 characters.
  
- `base_url` The dialogflow REST endpoint to which your query will be sent, Example: `https://dialogflow.googleapis.com/v2beta1`

- `service_account_key` access token linked with your project,Requires one of the following OAuth scopes:
`https://www.googleapis.com/auth/cloud-platform`
`https://www.googleapis.com/auth/dialogflow`
For more information, see the [Authentication Overview](https://cloud.google.com/docs/authentication/). In this example we will be passing dialogflow client access token in the auth header like so `Authorisation: Bearer <access token>.`

### Authentication 
To obtain the access token, follow the steps below:
1. Set up a [GCP Console](https://console.cloud.google.com) project.  
    - Create or select a project.  
    - Create a service account.  
    - Download a private key as JSON.
2. Set the environment variable GOOGLE_APPLICATION_CREDENTIALS to the file path of the JSON file that contains your service account key. This variable only applies to your current shell session, so if you open a new session, set the variable again.
Example: **Linux or macOS**

Replace **[PATH]** with the file path of the JSON file that contains your service account key.
```
export GOOGLE_APPLICATION_CREDENTIALS="[PATH]"
```
For example:
```
export GOOGLE_APPLICATION_CREDENTIALS="/home/user/Downloads/service-account-file.json"
```
3. [Install and initialize the Cloud SDK](https://cloud.google.com/sdk/docs/)
4. Print your access token 
```
echo $(gcloud auth application-default print-access-token)
```
which should output something like 
```
ya29.c.Kl6iB-r90Gjj4o--m7k7wr4dN4b84U4TLEtPqdEZ2xvfsj01awmUObMDEFwJIJ1lkZPym5dsAw44MbZDSaksLH3xKbsSHGLgWeEXqIPSDmFO6
```

### 3. Initialise the chatbot module
Now, we need to initialise our module by filling in the configuration details- module version, the name of our module and  the references of our module callback functions.

For a simple chatbot, we need to provide a callback function reference for `on_message`.  
`chabot_on_message` : To get input query from a message. This function needs to be called whenever there is an incoming message and alert us. So, we pass this function reference to be the module's `on_message` callback function.

Following the naming convention of for the init function whcih is `mesibo_module_<module name>_init` the initialisation function for the module `chatbot` will be as defined below.

```cpp
//File : chatbot.cpp
int mesibo_module_chatbot_init(mesibo_int_t version, mesibo_module_t *m, mesibo_uint_t len, module_configs_t *config) {

	if(MESIBO_MODULE_VERSION != version) {
		m->log(m, 0, "module version mismatch\n");
		return MESIBO_RESULT_FAIL; 
	}

	if (sizeof(mesibo_module_t) != len) {
		m->log(m, 0, "module size mismatch\n");
		return MESIBO_RESULT_FAIL;
	}

	if (MESIBO_MODULE_SIGNATURE != m->signature) {
		m->log(m, 0, "module signature mismatch\n");
		return MESIBO_RESULT_FAIL;
	}

	if(config) {
		m->log(m, 0, "Following configuration item(s) were passed to the module\n");
		for(int i=0; i < config->count; i++) {
			m->log(m, 0, "module config item: name %s value %s\n", 
					config->items[i].name, config->items[i].value);
		}
		chatbot_config_t* cbc = get_config_dialogflow(m, config);
		if(cbc  == NULL){
			m->log(m, 0, "Error: DialogFlow not configured , " 
					"Please provide details as per format in the configuration file");
			return MESIBO_RESULT_FAIL;
		}
		int init_status= chatbot_init_dialogflow(m, cbc);
		if(init_status!=MESIBO_RESULT_OK){
			m->log(m, 0, "Error:Chatbot Module Configuration for Dialogflow failed, "
					"Please check configuration \n");
			return MESIBO_RESULT_FAIL;
		}

		m->ctx = (void* )cbc;
	}

	else {
		m->log(m, 0, "Error: DialogFlow not configured , Please provide details as per format in the configuration file");
		return -1;
	}


	m->version = 1;
	m->flags = 0;
	m->name = strdup("Sample Chatbot Module");
	m->on_message = chatbot_on_message;
	m->on_login = chatbot_on_login;
	return 0;
```
### Storing the configuration in module context
All the configuration items that you have passed in `mesibo.conf` will be available as a list of name-value pairs, which can be accessed in the init function as shown below
```cpp
if(config) {
		m->log(m, 0, "Following configuration item(s) were passed to the module\n");
		for(int i=0; i < config->count; i++) {
			m->log(m, 0, "module config item: name %s value %s\n", 
					config->items[i].name, config->items[i].value);
		}
 }
```
We need to store this configuration for further use. To do this we use a structure called `chatbot_config_t` which is defined as follows:
```cpp
typedef struct chatbot_config_s {
	/* To be configured in module configuration file */
	char* project;
	char* session;
	char* base_url;
	char* service_account_key;

	/* To be configured by dialogflow init function */
        char* post_url;
        char* post_data;
        char* auth_bearer;
} chatbot_config_t;
```

To get the configuaration details stored into `chatbot_config_t`, you can use the config helper function `get_config_dialogflow` 

```cpp
		chatbot_config_t* cbc = get_config_dialogflow(m, config);
		if(cbc  == NULL){
			m->log(m, 0, "Error: DialogFlow not configured , " 
					"Please provide details as per format in the configuration file");
			return MESIBO_RESULT_FAIL;
		}
```    
`get_config_dialogflow` fetches all the configuration details as per format and stores into the appropriate members of the structure `chatbot_config_t`
```cpp
chatbot_config_t*  get_config_dialogflow(mesibo_module_t* mod, module_configs_t *config){
	chatbot_config_t* cbc = (chatbot_config_t*)calloc(1, sizeof(chatbot_config_t));
	cbc->project = mesibo_module_get_config_item(mod, config, "project");
	cbc->session = mesibo_module_get_config_item(mod, config, "session");
	cbc->base_url = mesibo_module_get_config_item(mod, config, "base_url");
	cbc->service_account_key = mesibo_module_get_config_ite(mod, config, "service_account_key");

	mod->log(mod, 0, "Configured DialogFlow :\n project %s \n session %s \n base_url %s\n service_account_key %s\n", cbc->project, cbc->session, cbc->base_url, cbc->service_account_key);

	return cbc;
}
```
### Initialising the request parmaters
The chatbot module will interact with the Google dialogflow model over REST API. This example uses `Dialogflow V2 API` and the [detectIntent](https://cloud.google.com/dialogflow/docs/reference/rest/v2/projects.agent.sessions/detectIntent) method to process queries. 

Once we get the configuration details, we can construct the request parameters such as the post url (endpoint), base post data format and the authorisation header. To do this we call the `chatbot_init_dialogflow` function as follows
```cpp
static int chatbot_init_dialogflow(mesibo_module_t* mod, chatbot_config_t* cbc){
	mod->log(mod, 0, "chatbot_init_dialogflow called \n");

	mod->log(mod, 0, "Constructing POST url \n");
	size_t post_url_len = strlen(cbc->base_url)+strlen("/projects/")+strlen(cbc->project)+strlen("/agent/sessions/")
		+strlen(cbc->session)+strlen(":detectIntent")+ 1 ;
	char* post_url = (char*)malloc(post_url_len);
	strcat(post_url, cbc->base_url);
	strcat(post_url, "/projects/");
	strcat(post_url, cbc->project);
	strcat(post_url, "/agent/sessions/");
	strcat(post_url, cbc->session);
	strcat(post_url, ":detectIntent");
	cbc->post_url = post_url;
	mod->log(mod, 0, "Configured post url for HTTP requests: %s \n", cbc->post_url);
        
	mod->log(mod, 0, "Constructing auth bearer \n");
	size_t auth_buff_len = strlen("Authorization: Bearer ")+strlen(cbc->service_account_key)+1;
        char* auth_buff = (char*)malloc(auth_buff_len);
        strcat(auth_buff, "Authorization: Bearer ");
        strcat(auth_buff, cbc->service_account_key);
        cbc->auth_bearer  = auth_buff;
        mod->log(mod, 0, "Configured auth bearer for HTTP requests with token: %s \n", cbc->auth_bearer );

        return MESIBO_RESULT_OK;
}
```

### 3.`chatbot_on_message`

We need message containing queries from the end user only. `on_message` will alert you for any message sent or received. So, we filter these messages to match only those messages that are incoming from a particular user `bot_user`. 
For incoming messages from `bot_user` we process the message/query and send a response from the chat-bot.
For all other messages we pass the message as it is.
```cpp
static mesibo_int_t chatbot_on_message(mesibo_module_t *mod, mesibo_message_params_t *p,
		const char *message, mesibo_uint_t len) {
	mod->log(mod, 0, " %s on_message called\n", mod->name);
	mod->log(mod, 0, " aid %u from %s to %s id %u message %s\n", p->aid, p->from,
			p->to, (uint32_t)p->id, message);

	mod->log(mod, 0, " Processing message from %s \n", p->from);
	// Don't modify original as other module will be use it
	mesibo_message_params_t *np =
		(mesibo_message_params_t *)calloc(1, sizeof(mesibo_message_params_t));
	memcpy(np, p, sizeof(mesibo_message_params_t));
	chatbot_process_message(mod, np, message, len);
	return MESIBO_RESULT_CONSUMED;  // Process the message and CONSUME original
}

```
### 4. Processing the incoming message
To process the incoming query we need to send it to a chatbot model. Here, as an example we will be using [Dialogflow](https://dialogflow.com) as the chatbot provider and will make a an HTTP request to your Dialogflow chatbot. Your chatbot will use it's dialogflow model and send the appropriate respoonse to your request.

For more info on using Dialogflow and building chatbot models, checkout [Dialogflow Docs](https://dialogflow.com/docs).

To send an HTTP request to your chatbot endpoint,the POST data must be in a specific input format as defined by your chatbot API . 

### Dialogflow V2 API 
In the case of dialogflow v2 API,
the request url is  `https://dialogflow.googleapis.com/v2beta1/{session=projects/*/agent/sessions/*}:detectIntent`
and the POST data will contain the [detectIntent](https://cloud.google.com/dialogflow/docs/reference/rest/v2beta1/projects.agent.sessions/detectIntent) string.
You also need to pass authentication information(your service account key) in the request header.

An HTTP [query sample for dialogflow v2 API](https://cloud.google.com/dialogflow/docs/reference/rest/v2beta1/projects.agent.sessions/detectIntent) looks like below
```
 POST https://dialogflow.googleapis.com/v2beta1/{session=projects/*/agent/sessions/*}:detectIntent

  Headers:
  Authorization: Bearer YOUR_SERVICE_ACCOUNT_KEY
  Content-Type: application/json

  POST body:
 {
  "queryParams": {
    object (QueryParameters)
  },
  "queryInput": {
    object (QueryInput)
  },
  "outputAudioConfig": {
    object (OutputAudioConfig)
  },
  "inputAudio": string
}
    
```
Following this POST format we send an HTTP request using the function `http`. 

On recieving the response in the http callback function `bot_http_callback`(which we shall define in the next step), from Dialogflow we need to send the response back to the user who made the request. So we store the context of the received message ie; message parameters ,the sender of the message,the reciever of the message in the following structure and pass it as callback data. Note that you can store any data that you require to be passed to the http_callback function by modifying the tMessageContext structure accordingly.

```cpp
static int chatbot_process_message(mesibo_module_t *mod, mesibo_message_params_t *p,
		const char *message, mesibo_uint_t len) {
	mod->log(mod, 0, "Processing message from %s \n", p->from);
	/**
	 *
	 * DialogFlow V2
	 * Dialogflow API reference https://cloud.google.com/dialogflow/docs
	 * POST
	 * https://dialogflow.googleapis.com/v2beta1/{session=projects/<project>/agent/sessions/<session id>}:detectIntent
	**/
	chatbot_config_t* cbc = (chatbot_config_t*)mod->ctx;
	const char* post_url = cbc->post_url; 
	mod->log(mod, 0, "Constructing request body \n");
	char *request_body ="{\"queryParams\":{\"timeZone\":\"Asia/Calcutta\"}, \"queryInput\":{\"text\":{\"text\":\"";
	mod->log(mod, 0, "Constructing request body \n");
	size_t request_body_len = strlen("{\"queryParams\":{\"timeZone\":\"Asia/Calcutta\"}, \"queryInput\":{\"text\":{\"text\":\"\", \"languageCode\":\"en\"}}}\0")+len+1;
	
	mod->log(mod, 0, "Constructing request body \n");
	char *raw_post_data = (char *)malloc(request_body_len);
	strcat(raw_post_data, request_body);
	mod->log(mod, 0, "Constructing request body \n");
	strncat(raw_post_data, message, len);
	strcat(raw_post_data, "\", \"languageCode\":\"en\"}}}\0");
	
	module_http_option_t *request_options =
		(module_http_option_t *)calloc(1, sizeof(module_http_option_t));

	/**
	 * Your dialogFlow service account key
	 * https://cloud.google.com/docs/authentication/
	**/
	request_options->extra_header = cbc->auth_bearer;
	request_options->content_type = "application/json";

	message_context_t *message_context =
		(message_context_t *)calloc(1, sizeof(message_context_t));
	message_context->mod = mod;
	message_context->params = p;
	message_context->from = strdup(p->from);
	message_context->to = strdup(p->to);

	mod->log(mod, 0, "http POST request %s %s %s \n", post_url, raw_post_data, request_options->extra_header);

	mod->http(mod, post_url, raw_post_data, chatbot_http_callback,
			(void *)message_context, request_options);

	return 0;
}
```                     
### 5. Define the Callback function to receive the response from your bot
In the callback function, we will be getting the response in asynchronous blocks. So,we copy the response data into a temporary buffer and once the complete response is received(indicated by progress=100) we send the response back to user who sent the query.

```cpp
static int chatbot_http_callback(void *cbdata, mesibo_int_t state,
		mesibo_int_t progress, const char *buffer,
		mesibo_int_t size) {
	message_context_t *b = (message_context_t *)cbdata;
	mesibo_module_t *mod = b->mod;
	mesibo_message_params_t *params = b->params;
	mod->log(mod, 0,
			" ===> message context parameters :aid %u id %u from %s to %s \n",
			params->aid, params->id, params->from, params->to);

	mod->log(mod, 0, "===> progress %d state %d size %d\n", (int)progress,
			(int)state, (int)size);

	if (progress < 0) {
		mod->log(mod, 0, " Error in http callback \n");
		free(b);
		return -1;
	}

	if (state != MODULE_HTTP_STATE_RESPBODY) {
		mod->log(mod, 0, " Exit http callback\n");
		if(size)
			mod->log(mod, 0, " Exit http callback %.*s \n", size, buffer);
		free(b);
		return 0;
	}

	if ((progress > 0) && (state == MODULE_HTTP_STATE_RESPBODY)) {
		memcpy(b->buffer + b->datalen, buffer, size);
		b->datalen += size;
	}

	if (progress == 100) {
		mod->log(mod, 0, "%.*s", b->datalen, b->buffer);
		mesibo_message_params_t *p =
			(mesibo_message_params_t *)calloc(1, sizeof(mesibo_message_params_t));
		p->id = rand();
		p->refid = params->id;
		p->aid = params->aid;
		p->from = b->to;
		p->to = b->from;  // User adress who sent the query is the recipient
		p->expiry = 3600;

		mod->log(mod, 0,
				" ===> Sending response :aid %u id %u from %s to %s  len %d\n",
				p->aid, p->id, b->from, b->to, b->datalen);
		mod->send_message(mod, p, b->buffer, (mesibo_uint_t)b->datalen);

		free(b);
	}

	return 0;
}

```
### 6. Compiling your module
To compile your module,Copy the sample `MakeFile` provided. Change the `TARGET` to `/usr/lib64/mesibo/mesibo_mod_<module_name>.so` or to a file path of your choice. 
For example.
```
/usr/lib64/mesibo/mesibo_mod_chatbot.so
```
Run the below command from your source directory. Before running make, ensure that all your source files are in the correct directory.
```
sudo make
```
 On successful build of your module, verify that the target path should contain your shared library `/usr/lib64/mesibo/mesibo_mod_chatbot.so`
 
 ### 7. Loading your module
 To load your chatbot module, specify the module name in mesibo configuration file `/etc/mesibo/mesibo.conf` like so
```
apptoken= f4awffepi012zbjwbpcoifchs8a4tetup6r9774326zjxcogij47goxxxxxx
module=chatbot{
project=mesibo-chatbot
session=4e72c746-7a38-66b6-xxxxx
base_url=https://dialogflow.googleapis.com/v2beta1
service_account_key=ya29.c.Kl6iB-r90Gjj4oxxxx-m7k7wr4dN4b84U4TLEtPqdEZ2xvfsj01awmUObMDEFwJIJ1lkZPym5dsAw44MbZDSaksLH3xKbsSHGLgWeEXqI-xxxxxx
}
 ```
 Mount the directory containing your library which in this case is `/usr/lib64/mesibo/`, while running the mesibo container as follows. You also need to mount the directory containing the mesibo configuration file which in this case is `/etc/mesibo`
 ```
sudo docker run  -v /certs:/certs -v  /usr/lib64/mesibo/:/usr/lib64/mesibo/ \
-v /etc/mesibo:/etc/mesibo
-net=host -d  \ 
mesibo/mesibo <app token> 
```
