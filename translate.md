#  Translation Module

This repository contains the source code for Translate Module. The translate module translates each message before sending it to destination.   

To perform translation, you can use any Translation API of your choice such as Google Translate, IBM Watson language Translator, Microsoft Azure Translate, Alibaba Cloud Translation, etc through REST endpoints.
This  Sample Translate Module provides an example using **Google Translate**

You can download the source and compile it to obtain the module- a shared library file. Also, you can load the pre-compiled module which is provided as `mesibo_mod_translate.so`

Refer to the [Skeleton Module](https://github.com/Nagendra1997/mesibo-documentation/blob/master/skeleton.md) for a basic understanding of how Mesibo Modules work. The complete documentation for Mesibo Modules is available [here](https://mesibo.com/documentation/loadable-modules/)

## Overview of  Translate Module
-  Translation configuration containing : Endpoint/base url for making REST call, target language, source language, access token (Service account key  for authenticating your requests) is provided in the module configuration (In the file `mesibo.conf`).
- In module initialisation, all the configuration parameters is obtained from the configuration list and stored in a structure object  `translate_config_t`. The authorisation header is constructed and stored in the form 
`" Authorisation : Bearer <access token> "`
- In module initialisation,The callback function for `on_message` is initialized to  `translate_on_message`
- When a message is sent from a user, Mesibo invokes the `translate_on_message` callback function of the module with the message data and it’s associated message parameters such as sender, expiry, flags, etc.
- Now, the translate module makes an HTTP request to a REST endpoint of Google Translate .The HTTP Post data contains the message text, target language and source language. The authorisation header containing the access token is also sent in the POST request.
- Google Translate sends the response with the text translated to target language (a JSON string), which is recieved through an http callback function.
- The orginal message is CONSUMED and the translated message is sent to the recipient. (In this example, The entire JSON response string from Google Translate is sent to the recipient)

## Building the Translate Module

### 1. The C/C++ Source file
The module name is `translate`. The C/C++ Source file is `translate.cpp`. The header file `module.h` containing the definitions of all module related components is included in the C/C++ source as follows:
```cpp
#include "module.h"
```
### 2. Configuring the translate module
While loading your module you need to provide your configuration details in the file `/etc/mesibo/mesibo.conf`. The sample configuration is provided in the file `translate.conf`. Copy the configuration from `translate.conf`into `/etc/mesibo/mesibo.conf`

The translate module is configured as follows:

```

module=translate{
endpoint=https://translation.googleapis.com/language/translate/v2
access_token=<access token>
source=<Source Language Code>
target=<Target Language Code>
}


```

For example, To translate from English(`en`) to German(`de`) the configuration looks like:
```
module=translate{
endpoint=https://translation.googleapis.com/language/translate/v2
access_token=xxxxxx.Kl6iBzVH7dvV2XywzpnehLJwczdClfMoAHHOeTVNFkmTjqVX7VagKHH1-xxxxxxx
source=en
target=de
}

```

- `endpoint` The Google Translate REST endpoint to which your message will be sent and translated, Example: `https://translation.googleapis.com/language/translate/v2`

- `access_token` access token linked with your project,Requires one of the following OAuth scopes:
`https://www.googleapis.com/auth/cloud-translation`
`https://www.googleapis.com/auth/cloud-platform`
For more information, see the [Authentication Overview](https://cloud.google.com/docs/authentication/). In this example we will be passing Google Translate access token in the auth header like so `Authorisation: Bearer <access token>.`

- `source` Source Language code from which the text needs to be translated
- `target` Target language code into which the text needs  to be translated 

Language code parameters conform to [ISO-639-1](https://en.wikipedia.org/wiki/ISO_639-1) identifiers, except where noted. Refer [supported languages](https://cloud.google.com/translate/docs/languages)   

### Authentication 
To obtain the access token, follow the steps below:
1. Set up a [GCP Console](https://console.cloud.google.com) project.  
    - Create or select a project. 
    - Enable the Cloud Translation API for that project.
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
Also, Refer [Quickstart](https://cloud.google.com/translate/docs/quickstart) which explains how to make a Cloud Translation API request with curl.

### 3. Initializing the translate module
The translate module is initialized with the Mesibo Module Configuration details - module version, the name of the module and  references to the module callback functions.
```cpp

int mesibo_module_translate_init(mesibo_int_t version, mesibo_module_t *m, mesibo_uint_t len, module_configs_t *config) {

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
		translate_config_t* tc = get_config_google_translate(m, config);
		if(tc  == NULL){
			m->log(m, 0, "Error: Google Translate not configured , " 
					"Please provide details as per format in the configuration file");
			return MESIBO_RESULT_FAIL;
		}
		int init_status= translate_init_google(m, tc);
		if(init_status!=MESIBO_RESULT_OK){
			m->log(m, 0, "Error:Translate Module Configuration for Google Translate failed, "
					"Please check configuration \n");
			return MESIBO_RESULT_FAIL;
		}

		m->ctx = (void* )tc;
	}

	else {
		m->log(m, 0, "Error: Google Translate not configured , Please provide details as per format in the configuration file");
		return -1;
	}

	m->version = 1;
	m->flags = 0;
	m->name = strdup("Sample Translate Module");
	m->on_message = translate_on_message;
	m->on_login = translate_on_login;
	return 0;
}

```
The translation configuration is obtained from the configuration list and the callback function for `on_message` is initialized to `translate_on_message`.

### Storing the translate configuration in module context
The profanity list is extracted from module configuration list and stored in the configuration context structure `translate_config_t` which is defined as follows:
```cpp

typedef struct translate_config_s {
	/* To be configured in module configuration file */
	char* access_token;
	char* endpoint;
	char* source;
	char* target;

	/* To be configured by Google translate init function */
	char* post_url;
	char* auth_bearer;
} translate_config_t;

```

To get the configuaration details the config helper function `get_config_google_translate` is called.

```cpp

translate_config_t*  get_config_google_translate(mesibo_module_t* mod, module_configs_t *config){
	translate_config_t* tc = (translate_config_t*)calloc(1, sizeof(translate_config_t));
	tc->endpoint = mesibo_module_get_config_item(mod, config, "endpoint");
	tc->source = mesibo_module_get_config_item(mod, config, "source");
	tc->target = mesibo_module_get_config_item(mod, config, "target");
	tc->access_token = mesibo_module_get_config_item(mod, config, "access_token");

	mod->log(mod, 0, " Configured Google Translate :\n endpoint %s \n source %s \n target %s\n access_token %s\n", 
		tc->endpoint, tc->source, tc->target, tc->access_token);

	return tc;

}

```    
A pointer to the translate configuration is stored in `m->ctx`

### Initialising the request parmaters
The translate module will interact with the Google Translate Service over REST API. This example uses [Cloud Translate API v2](https://cloud.google.com/translate/docs/translating-text) method to translate text . 

Once we get the configuration details, we can construct the request parameters such as the post url (endpoint) and the authorisation header. To do this we call the `translate_init_google` function as follows:
```cpp

static int translate_init_google(mesibo_module_t* mod, translate_config_t* tc){
	mod->log(mod, 0, "translate_init_google called \n");

	mod->log(mod, 0, "Constructing POST url \n");
	tc->post_url = tc->endpoint;
	mod->log(mod, 0, "Configured post url for HTTP requests: %s \n", tc->post_url);

	mod->log(mod, 0, "Constructing auth bearer \n");
	size_t auth_buff_len = strlen("Authorization: Bearer ")+strlen(tc->access_token)+1;
	char* auth_buff = (char*)malloc(auth_buff_len);
	strcat(auth_buff, "Authorization: Bearer ");
	strcat(auth_buff, tc->access_token);
	tc->auth_bearer  = auth_buff;
	mod->log(mod, 0, "Configured auth bearer for HTTP requests with token: %s \n", tc->auth_bearer );

	return MESIBO_RESULT_OK;
}

```

### 4. translate_on_message
`translate_on_message` will notify the translate module when a message is sent from a user. The translate module intercepts each message , translates the message and sends the translated text to the recipient. On recieving a message ,`translate_process_message` is called For translating the message which makes an HTTP request to a Google Translate Service REST endpoint. The response from Google Translate containing the translated text is recieved through an http callback function and is sent to the recipient.


```cpp

static mesibo_int_t translate_on_message(mesibo_module_t *mod, mesibo_message_params_t *p,
		const char *message, mesibo_uint_t len) {
	mod->log(mod, 0, " %s on_message called\n", mod->name);
	mod->log(mod, 0, " aid %u from %s to %s id %u message %s\n", p->aid, p->from,
			p->to, (uint32_t)p->id, message);

	mod->log(mod, 0, " Processing message from %s \n", p->from);
	// Don't modify original as other module will be use it
	mesibo_message_params_t *np = (mesibo_message_params_t *)calloc(1, sizeof(mesibo_message_params_t));
	memcpy(np, p, sizeof(mesibo_message_params_t));
	translate_process_message(mod, np, message, len);
	return MESIBO_RESULT_CONSUMED;  // Process the message and CONSUME original message
}

```

### 5. Translating the message

In the case of [Cloud Translate API](https://cloud.google.com/translate/docs/quickstart),
the base url is  `https://translation.googleapis.com/language/translate/v2`. You also need to pass authentication information(your service account key) in the request header.

An [example REST request and response of Google Translate Service](https://cloud.google.com/translate/docs/translating-text) looks like below:
```

POST https://translation.googleapis.com/language/translate/v2

```
Request JSON body:
```

{
  "q": ["Hello world", "My name is Jeff"],
  "target": "de"
}

```
You should see a JSON response similar to the following:
```

{
  "data": {
    "translations": [
      {
        "translatedText": "Hallo Welt",
        "detectedSourceLanguage": "en"
      },
      {
        "translatedText": "Mein Name ist Jeff",
        "detectedSourceLanguage": "en"
      }
    ]
  }
}

```
### translate_process_message

Get the translate configuration from module context `mod->ctx` (Casting from `void*` to `filter_config_t*` ).  Following this [POST request format](https://cloud.google.com/translate/docs/translating-text) we send an HTTP request using the function `http`. We also pass the authorisation header containing the access token (Service Account Key)

On recieving the response in the http callback function `bot_http_callback`(which we shall define in the next step), from Google Translate we need to send the response back to the recipient. So we store the context of the received message ie; message parameters ,the sender of the message,the reciever of the message in the following structure and pass it as callback data. Note that you can store any data that you require to be passed to the http callback function by modifying the `tMessageContext` structure accordingly.


```cpp

static int translate_process_message(mesibo_module_t *mod, mesibo_message_params_t *p,
		const char *message, mesibo_uint_t len) {
	mod->log(mod, 0, "Processing message from %s \n", p->from);
	/**
	 *
	 * Google Translate V2
	 * API reference https://cloud.google.com/translate/docs/quickstart 
	 * POST
	 * https://translation.googleapis.com/language/translate/v2
	 **/
	translate_config_t* tc = (translate_config_t*)mod->ctx;
	const char* post_url = tc->post_url; 
	mod->log(mod, 0, "Constructing post body \n");
	char *post_body = (char*)"{\"q\":[\"\"], \"target\":\"\"}";
	size_t post_body_len = strlen(post_body)+strlen(tc->target)+len+1;

	char *raw_post_data = (char *)malloc(post_body_len);
	strcat(raw_post_data, "{\"q\":[\"");
	strncat(raw_post_data, message, len);
	strcat(raw_post_data, "\"], \"target\":\"");
	strcat(raw_post_data, tc->target);
	strcat(raw_post_data, "\"}");	

	module_http_option_t *request_options =
		(module_http_option_t *)calloc(1, sizeof(module_http_option_t));

	/**
	 * Your Google Cloud Application service account key
	 * https://cloud.google.com/docs/authentication/
	 **/
	request_options->extra_header = tc->auth_bearer;
	request_options->content_type = "application/json";

	message_context_t *message_context =
		(message_context_t *)calloc(1, sizeof(message_context_t));
	message_context->mod = mod;
	message_context->params = p;
	message_context->from = strdup(p->from);
	message_context->to = strdup(p->to);

	mod->log(mod, 0, "http POST request %s %s %s \n", post_url, raw_post_data, request_options->extra_header);

	mod->http(mod, post_url, raw_post_data, translate_http_callback,
			(void *)message_context, request_options);

	return 0;
}

```


Returns:   
`MESIBO_RESULT_PASS` If no profanity was found . The message is safely sent to the recipient
 OR
`MESIBO_RESULT_CONSUMED` If the message is found to contain profinity. The unsafe message is dropped and prevented from reaching the receiver.

### 5. Compiling the filter module

To compile the filter module from source run
```
make
```
from the source directory which uses the sample `Makefile` provided to build a shared object `mesibo_mod_filter.so`. It places the result at the `TARGET` location `/usr/lib64/mesibo/mesibo_mod_filter.so` which you can verify.

### 6. Loading the filter module 
You can load the pre-compiled module (.so) by specifying the matching configuration in `mesibo.conf` as provided in `filter.conf` and mount the directory which contains the module shared library while running the mesibo container.

If you are loading a pre-compiled module make sure that you have mounted the path to the .so file. If `mesibo_mod_filter.so` is located at `/path/to/mesibo_mod_filter.so`,you should mount the directory as 
```
 -v path/to/mesibo_mod_filter.so:/path/to/mesibo_mod_filter.so

```

For example, if `mesibo_mod_filter.so` is located at `/usr/lib64/mesibo/`
```
sudo docker run  -v /certs:/certs -v  /usr/lib64/mesibo/:/usr/lib64/mesibo/ \
-v /etc/mesibo:/etc/mesibo
-net=host -d  \ 
mesibo/mesibo <app token>
