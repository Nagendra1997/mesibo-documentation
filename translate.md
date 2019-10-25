#  Translation Module

This repository contains the source code for Translate Module. The translate module translates each message before sending it to destination. 
To perform translation, you can use any Translation API of your choice such as Google Translate, IBM Watson language Translator, Microsoft Azure Translate, Alibaba Cloud Translation, etc through REST endpoints.
This  Sample translate Module provides an example using **Google Translate**
You can download the source and compile it to obtain the module- a shared library file. Also, you can load the pre-compiled module which is provided as `mesibo_mod_translate.so`
Refer to the [Skeleton Module](https://github.com/Nagendra1997/mesibo-documentation/blob/master/skeleton.md) for a basic understanding of how Mesibo Modules work. The complete documentation for Mesibo Modules is available [here](https://mesibo.com/documentation/loadable-modules/)
## Overview of  Translate Module
-  Translation configuration containing : Endpoint/base url for making REST call, target language, source language, access token (Service account key  for authenticating your requests) is provided in the module configuration (In the file `mesibo.conf`).
- In module initialisation, all the configuration parameters is obtained from the configuration list and stored in a structure object  `translate_config_t`. The authorisation header is constructed and stored in the form " Authorisation : Bearer <access token> "
- In module initialisation,The callback function for `on_message` is initialized to  `translate_on_message`
- When a message is sent from a user, Mesibo invokes the `translate_on_message` callback function of the module with the message data and it’s associated message parameters such as sender, expiry, flags, etc.
- Now, the translate module makes an HTTP request to a REST endpoint of Google Translate .The HTTP Post data contains the message text, target language and source language. The authorisation header containing the access token is also sent in the POST request.
- Google Translate sends the response with the text translated to target language (a JSON string), which is recieved through an http callback function.
- The orginal message is CONSUMED and the translated message

## Building the Filter Module

### 1. The C/C++ Source file
The module name is `filter`. The C/C++ Source file is `filter.cpp`. The header file `module.h` containing the definitions of all module related components is included in the C/C++ source as follows:
```cpp
#include "module.h"
```
### 2. Configuring the filter module
While loading your module you need to provide your configuration details in the file `/etc/mesibo/mesibo.conf`. The example configuration is provided in the file `filter.conf`. Copy the configuration from `filter.conf`into `/etc/mesibo/mesibo.conf`

The filter module is configured as follows:
- The number of blocked words/profanity is provided in `count`
- The list of blocked words/profanity is provided as list of comma seperated values. 
ie;`blocked_word_1`, `blocked_word_2`, `blocked_word_3`, ... `blocked_word_N`. Note the space after each comma. 
```

module=filter{
count=3
blocked_words=alpha, beta, gamma 
}

```
### 3. Initializing the filter module
The filter module is initialized with the Mesibo Module Configuration details - module version, the name of the module and  references to the module callback functions.
```cpp

int mesibo_module_filter_init(mesibo_int_t version, mesibo_module_t *m, 
                              mesibo_uint_t len, module_configs_t *config){
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

	m->version = 1;
	m->flags = 0;
	m->name = strdup("Sample Filter Module");
	m->on_message = filter_on_message;

	if(config){
		filter_config_t* fc = get_filter_config(m, config);
		if(fc == NULL){
			m->log(m, 0, "Filter not configured. Please configure as per format in configuration file\n");
			return MESIBO_RESULT_FAIL;
		}
		m->ctx = (void*)fc;
	}
	else {
		m->log(m, 0, "Filter not configured. Please configure as per format in configuration file\n");
		return MESIBO_RESULT_FAIL;
	}

	m->cleanup = filter_cleanup; //Cleanup memory on exit
	return 0;
}

```
The profanity list is obtained from the configuration items list and the callback function for `on_message` is initialized to  `filter_on_message`

### Storing the configuration in module context
The profanity list is extracted from module configuration list and stored in the configuration context structure `filter_config_t` which is defined as follows:
```cpp

typedef struct filter_config_s{
	int  count;
	char** blocked_words;
}filter_config_t;

```

To get the configuaration details the config helper function `get_config_filter` is called.
The list of blocked words is a single string (It is a `value` in the list of name-value pairs in configuration items. `blocked_words` is the `name` and the list of comma seprated blocked words is the `value` which is stored as a SINGLE string). `strtok` breaks down the string into a sequence of tokens. We traverse through the sequence and store each blocked word in an array.
```cpp

filter_config_t* get_config_filter(mesibo_module_t* mod, module_configs_t* config){
	filter_config_t* fc = (filter_config_t*)calloc(1, sizeof(filter_config_t));
	fc->count = atoi(mesibo_module_get_config_item(mod, config, "count")); //Number of blocked words
	char* bw = mesibo_module_get_config_item(mod, config, "blocked_words"); //comma seperated blocked words	
	if(fc->count <= 0){
		mod->log(mod, 0 , "Error: Incorrect configuration : count should be non zero and positive\n");
		return NULL;
	}

	fc->blocked_words = (char**)malloc(fc->count);

	int i = 0;	
	const char* delimitter = ", ";
	const char* token = strtok(bw, delimitter); // blocked_words: bw1, bw2, bw3, ...  
	while(token != NULL && i< fc->count){
		fc->blocked_words[i] = mesibo_strupr(strdup(token));
		mod->log(mod, 0, " blocked_word_%d: %s\n", i, fc->blocked_words[i]);
		token = strtok(NULL, delimitter);
		i++;
	}
	return fc;
}

```    
A pointer to the filter configuration is stored in `m->ctx`

### 4. filter_on_message
`filter_on_message` will notify the filter module when any message is exchanged between users. The filter module intercepts each message and decides what to do with it. It analyzes the message for profinity and decides to PASS the message as SAFE or CONSUME the message to prevent the unsafe message reaching the recipient. 

### Analyzing the message for profinity
Get the filter configuration from module context `mod->ctx` (Casting from `void*` to `filter_config_t*` ). Traverse the list of blocked words and check if the message contains the blocked word. This example is an extremely simplified implementation of a profanity filter. It is not a regex matching type filter, so it only detects profanity or blocked word if it is seen as an individual string. It cannot detect if the blocked word is a substring of a word.

If no profanity was found return `MESIBO_RESULT_PASS` and the message is safely sent to the recipient. If the message is found to contain profinity return `MESIBO_RESULT_CONSUMED` and the unsafe message is dropped and prevented from reaching the receiver.


```cpp

static mesibo_int_t filter_on_message(mesibo_module_t *mod, mesibo_message_params_t *p,
		const char *message, mesibo_uint_t len) {
	mod->log(mod, 0, " %s on_message called\n", mod->name);
	mod->log(mod, 0, " aid %u from %s to %s id %u message %s\n", p->aid, p->from,
			p->to, (uint32_t)p->id, message);
	
	char* in_message = strndup(message, len);
	in_message = mesibo_strupr(in_message) ;//Convert to UPPER_CASE
	
	filter_config_t* fc = (filter_config_t*)mod->ctx;
	mod->log(mod, 0, " Analyzing message %s \n", in_message );
	for(int i =0; i< fc->count ;i++){ //Loop through list of blocked words
		printf("Checking for blocked word %s \n", fc->blocked_words[i]);
		if(strstr(in_message, fc->blocked_words[i])){ //Message Contains blocked word 
			mod->log(mod, 0, "Message %s contains blocked word %s , "
					"Message shall be dropped\n", in_message, fc->blocked_words[i]);
			free(in_message);
			return MESIBO_RESULT_CONSUMED; 
			//drop message and prevent message from  reaching the recipient
		}
	}

	mod->log(mod, 0, " Message is SAFE to be passed \n");  
	free(in_message);
	return MESIBO_RESULT_PASS;  
	// PASS the message as it is, after checking that it is SAFE
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
