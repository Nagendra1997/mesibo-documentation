#  Chatbot Module


This repository contains the source code for Translate Module. Chatbot module analyzes messages using various AI and machine learning tools like Tensorflow, Dialogflow, etc. and sends an automatic reply.  

To build chatbots, you can use any chatbot provider of your choice such as Dialogflow, IBM Watson,Rasa,etc and connect with them through REST endpoints.
This  Sample Translate Module provides an example using **Dialogflow**

You can download the source and compile it to obtain the module- a shared library file. Also, you can load the pre-compiled module which is provided as `mesibo_mod_chatbot.so`

Refer to the [Skeleton Module](https://github.com/Nagendra1997/mesibo-documentation/blob/master/skeleton.md) for a basic understanding of how Mesibo Modules work. The complete documentation for Mesibo Modules is available [here](https://mesibo.com/documentation/loadable-modules/)

![Module Chatbot Sample](module_chat_bot.jpg)

## Overview of  Chatbot Module
-  Chatbot configuration containing :project name, session id, Endpoint/base url for making REST call, access token (Service account key  for authenticating your requests) is provided in the module configuration (In the file `mesibo.conf`).
- In module initialisation, all the configuration parameters is obtained from the configuration list and stored in a structure object  `chatbot_config_t`. The authorisation header is constructed and stored in the form 
`" Authorisation : Bearer <access token> "`
- In module initialisation,The callback function for `on_message` is initialized to  `chatbot_on_message`
- When a message is sent from a user to a particular user named `bot_endpoint`, Mesibo invokes the `chatbot_on_message` callback function of the module with the message data and it’s associated message parameters such as sender, expiry, flags, etc.
- Now, the chatbot module makes an HTTP request to a REST endpoint of Dialogflow .The HTTP Post body contains the message text as the `queryInput`. The authorisation header containing the access token is also sent in the POST request.
- Dialogflow sends a response from the chatbot, which is recieved through an http callback function.
- The orginal message is CONSUMED . The fulfillment text from the chatbot is extracted from the JSON response and is sent to the sender of the query in the form of an automatic reply. 

### 1. Create a C/C++ Source file
The module name is `chatbot`. The C/C++ Source file is `chatbot.cpp`. The header file `module.h` containing the definitions of all module related components is included in the C/C++ source as follows:
```cpp
#include "module.h"
```
### 2.Configuraing  the chatbot module
While loading your module you need to provide your configuration details in the file `/etc/mesibo/mesibo.conf`. The sample configuration is provided in the file `chatbot.conf`. Copy the configuration from `chatbot.conf`into `/etc/mesibo/mesibo.conf`

The chatbot module is configured as follows:
```

module=chatbot{
project=<project name>
session=<session id>
endpoint=<dialogfloe service endpoint>
access_token=<service account key>
chatbot_uid=<bot User address>
}

```
For example,
```

module=chatbot{
project=mesibo-chatbot
session=4e72c746-7a38-66b6-xxxxxx
endpoint=https://dialogflow.googleapis.com/v2beta1
access_token=xxxxxx.Kl6iBzVH7dvV2XywzpnehLJwczdClfMoAHHOeTVNFkmTjqVX7VagKHH1-xxxxxxx
chatbot_uid=my chatbot
}

```
### Configuration paramters:
- `project` is the Google Project name that contains your dialogflow chatbot. 
- `session` The name of the session this query is sent to. Format: projects/<Project ID>/agent/sessions/<Session ID>, or projects/<Project ID>/agent/environments/<Environment ID>/users/<User ID>/sessions/<Session ID>. If Environment ID is not specified, we assume default 'draft' environment. If User ID is not specified, we are using "-". It's up to the API caller to choose an appropriate Session ID and User Id. They can be a random number or some type of user and session identifiers (preferably hashed). The length of the Session ID and User ID must not exceed 36 characters.
  
- `endpoint` The dialogflow REST endpoint to which your query will be sent, Example: `https://dialogflow.googleapis.com/v2beta1`

- `access_token` access token linked with your project, Requires one of the following OAuth scopes:
`https://www.googleapis.com/auth/cloud-platform`
`https://www.googleapis.com/auth/dialogflow`
For more information, see the [Authentication Overview](https://cloud.google.com/docs/authentication/). In this example we will be passing dialogflow client access token in the auth header like so `Authorisation: Bearer <access token>.`

- `chatbot_uid` User address. In your Mesibo Application, create a user that you can refer to as a chatbot endpoint user.  

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

### 3. Initializing the chatbot module
The chatbot module is initialized with the Mesibo Module Configuration details - module version, the name of the module and  references to the module callback functions.
```cpp

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
		m->log(m, 0, "Error: DialogFlow not configured ,"
		"Please provide details as per format in the configuration file");
		return MESIBO_RESULT_FAIL;
	}


	m->version = 1;
	m->flags = 0;
	m->name = strdup("Sample Chatbot Module");
	m->on_message = chatbot_on_message;
	m->on_login = chatbot_on_login;
	return MESIBO_RESULT_OK;
```
The chatbot configuration is obtained from the configuration list and the callback function for `on_message` is initialized to `chatbot_on_message`.

### Storing the configuration in module context

The translation configuration parameters is extracted from module configuration list and stored in the configuration context structure `chatbot_config_t` which is defined as follows:

```cpp

typedef struct chatbot_config_s {
        /* To be configured in module configuration file */
        char* project;
        char* session;
        char* endpoint;
        char* access_token;
        char* chatbot_uid;

        /* To be configured by dialogflow init function */
        char* post_url;
        char* auth_bearer;
} chatbot_config_t;

```

To get the configuaration details , the config helper function `get_config_dialogflow` is called.

`get_config_dialogflow` fetches all the configuration details as per format and stores into the appropriate members of the structure `chatbot_config_t`
```cpp

chatbot_config_t*  get_config_dialogflow(mesibo_module_t* mod, module_configs_t *config){
        chatbot_config_t* cbc = (chatbot_config_t*)calloc(1, sizeof(chatbot_config_t));
        cbc->project = mesibo_module_get_config_item(mod, config, "project");
        cbc->session = mesibo_module_get_config_item(mod, config, "session");
        cbc->endpoint = mesibo_module_get_config_item(mod, config, "base_url");
        cbc->access_token = mesibo_module_get_config_item(mod, config, "access_token");
        cbc->chatbot_uid = mesibo_module_get_config_item(mod, config, "chatbot_uid");

        mod->log(mod, 0, "Configured DialogFlow :\n project %s \n session %s \n endpoint %s\n access_token %s\n 
                        chatbot_uid %s\n", cbc->project, cbc->session, cbc->base_url, cbc->access_token, 
                        cbc->chatbot_uid);
        
        return cbc;
}

```
A pointer to the chatbot configuration is stored in `m->ctx`

### Initialising the request parmaters
The chatbot module will interact with the Google dialogflow model over REST API. This example uses `Dialogflow V2 API` and the [detectIntent](https://cloud.google.com/dialogflow/docs/reference/rest/v2/projects.agent.sessions/detectIntent) method to process queries. 

Once we get the configuration details, we can construct the request parameters such as the post url (endpoint) and the authorisation header. To do this we call the `chatbot_init_dialogflow` function as follows
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

We only need message containing queries addressed to the uid configured in `chatbot_uid` . `chatbot_on_message` will alert you for any message sent . So, we filter these messages to match only those messages that are outgoing to a particular user corresponding to `chatbot_uid`. 
For incoming messages from `bot_user` we process the message/query and send a response from the chat-bot.
For all other messages we pass the message as it is.

```cpp

static mesibo_int_t chatbot_on_message(mesibo_module_t *mod, mesibo_message_params_t *p,
                const char *message, mesibo_uint_t len) {
        mod->log(mod, 0, " %s on_message called\n", mod->name);
        mod->log(mod, 0, " aid %u from %s to %s id %u message %s\n", p->aid, p->from,
                        p->to, (uint32_t)p->id, message);

        chatbot_config_t* cbc = (chatbot_config_t*)mod->ctx;
        if(strcmp(p->from,cbc->chatbot_uid) == 0){
                mod->log(mod, 0, " Processing message from %s \n", p->from);
                // Don't modify original as other module will use it
                mesibo_message_params_t *np =
                        (mesibo_message_params_t *)calloc(1, sizeof(mesibo_message_params_t));
                memcpy(np, p, sizeof(mesibo_message_params_t));
                chatbot_process_message(mod, np, message, len);
                return MESIBO_RESULT_CONSUMED;  // Process the message and CONSUME original
        }

        return MESIBO_RESULT_PASS;
}

```
### 4. Processing the query 
To process the incoming query we need to send it to a chatbot model. Here, as an example we will be using [Dialogflow](https://dialogflow.com) as the chatbot provider and will make a an HTTP request to your Dialogflow chatbot. Your chatbot will use it's dialogflow model and send the appropriate respoonse to your request.

For more info on using Dialogflow and building chatbot models, checkout [Dialogflow Docs](https://dialogflow.com/docs).

To send an HTTP request to your chatbot endpoint,the POST data must be in a specific input format as defined by your chatbot API . 

### Dialogflow V2 API 
In the case of dialogflow v2 API,
the request url is  `https://dialogflow.googleapis.com/v2beta1/{session=projects/*/agent/sessions/*}:detectIntent`
and the method to use is [detectIntent](https://cloud.google.com/dialogflow/docs/reference/rest/v2beta1/projects.agent.sessions/detectIntent) string.
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

The response is recieved through the http callback function `chatbot_http_callback`(which we shall define in the next step), We need to send the response back to the user who made the request. So we store the context of the received message ie; message parameters ,the sender of the message,the reciever of the message in the following structure and pass it as callback data. Note that you can store any data that you require to be passed to the http_callback function by modifying the tMessageContext structure accordingly.

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

	return MESIBO_RESULT_OK;
}
```                     
### 5. Define the Callback function to receive the response from your bot
In the callback function, we will be getting the response in asynchronous blocks. So,we copy the response data into a temporary buffer and once the complete response is received(indicated by progress=100) we send the response back to the user who sent the query.

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
		return  MESIBO_RESULT_FAIL;
	}

	if (state != MODULE_HTTP_STATE_RESPBODY) {
		mod->log(mod, 0, " Exit http callback\n");
		if(size)
			mod->log(mod, 0, " Exit http callback %.*s \n", size, buffer);
		free(b);
		return  MESIBO_RESULT_OK;
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

	return  MESIBO_RESULT_OK;
}

```
### 6. Compiling the chatbot module
To compile the chatbot module from source run
```
make
```
from the source directory which uses the sample `Makefile` provided to build a shared object `mesibo_mod_chatbot.so`. It places the result at the `TARGET` location `/usr/lib64/mesibo/mesibo_mod_chatbot.so` which you can verify.

 ### 7. Loading your module
 You can load the pre-compiled module (.so) by specifying the matching configuration in `mesibo.conf` as provided in `chatbot.conf` and mount the directory which contains the module shared library while running the mesibo container.

If you are loading a pre-compiled module make sure that you have mounted the path to the .so file. If `mesibo_mod_chatbot.so` is located at `/path/to/mesibo_mod_chatbot.so`,you should mount the directory as 
```
 -v path/to/mesibo_mod_translate.so:/path/to/mesibo_mod_chatbot.so

```

For example, if `mesibo_mod_chatbot.so` is located at `/usr/lib64/mesibo/`
```
sudo docker run  -v /certs:/certs -v  /usr/lib64/mesibo/:/usr/lib64/mesibo/ \
-v /etc/mesibo:/etc/mesibo
-net=host -d  \ 
mesibo/mesibo <app token>
