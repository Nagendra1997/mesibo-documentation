---
description: Loadable Modules - add your own custom features to Mesibo
keywords: open, source, on-premise, messaging, chat, voice, video, loadable modules
title: Loadable Modules - add your own custom features to Mesibo
---
Imagine, if you could have the ability to process every message between your users, or could add custom features and functionalities. For example, create chatbots, filter profanity, translate messages between sender and recipient, analyze messages with machine learning and AI tools, and more. This can open up a plethora of creative possibilities for your apps.  Now with mesibo modules, all of this is possible!.

Mesibo is designed “**by Developers for Developers**!”. As developers, we understand that a platform is very limited unless it allows its users to build more features and functionalities on it. This is how Mesibo loadable modules come in. 

Mesibo loadable modules let you expand mesibo by adding your own features and functionalities. You can build powerful chatbots, filters, remotely communicate with hardware for IoT and robotics, integrate with Machine learning and Scientific computing backend such as Tensorflow, Dialogflow, Matlab, etc. and much more, keeping your data secure and private in your own premises or private cloud.

This makes mesibo the most compelling real-time communication platform existing today. In this document, we will describe how you can build and use mesibo modules to unlock new possibilities and innovative solutions.

{% comment %} 
## Overview
0. [Prerequisites](#prerequisites)
1. [Overview of a mesibo module](#what-is-a-mesibo-module)   
a. [What is a mesibo module ](#what-is-a-mesibo-module)   
b. [How do mesibo modules work](#how-do-mesibo-modules-work)  
c. [Building a profanity filter](#building-a-profanity-filter)

2. [Anatomy of a Mesibo Module](#anatomy-of-a-mesibo-module)  
a. [Module Configuration Struct](#module-configuration-struct)  
b. [Module initialization](#module-initialization)  
c. [Callback Functions](#callback-functions)  
d. [Callable Functions](#callable-functions)  
e. [Data Structures](#data-structures)  
f. [Memory Management](#memory-management)

3. [Writing and Compiling Mesibo Modules](#writing-and-compiling-mesibo-modules) 
4. [Loading a Mesibo Module](#loading-a-mesibo-module)
5. [Code references and Examples](#code-references-and-examples)  

{% endcomment %}

## Prerequisites 
- [Running Mesibo On-Premise](https://mesibo.com/documentation/on-premise/)
- Knowledge of Building and deploying C/C++ shared libraries

{% comment %} 
## Ready-to-use modules Source Code
Github
{% endcomment %}

## What is a Mesibo Module?

Mesibo module is essentially a message processor that allows you to intercept each message and decide whether to pass the message to the destination as it is, drop it or process it before sending it to the destination. 

For example,

- a **profanity filter module** can drop messages containing profanity 
- a **translator module** can translate each message before sending it to the destination
- a **chatbot module** can analyze messages using various AI and machine learning tools like Tensorflow, Dialogflow, etc. and send an automatic reply 

The functionality of each module is programmed by you, and its capability is limited only by your imagination.  Mesibo modules make Mesibo a powerful communication platform.  

You can build a mesibo module on top of the core platform, as a shared library (a `.so` file) and load it to extend the functionality of the mesibo platform.

![Module Architecture](Mesibo_Loadable_Modules.jpg)

### How do Mesibo Modules work?
A Mesibo module is a shared library (`.so` file) that can be loaded at runtime by the Mesibo on-premise server. Mesibo then invokes various callback functions that you have defined in the shared library whenever it receives messages from your users. Your module can then decide what to do with those messages, for example:

 - Pass the message as it is
 - Drop it
 - Process it further before sending it to the destination
 - Reply to the sender
   
![Module Flowchart](module_flow_chart.jpg)

You can load multiple modules, each having their own features and functionalities. It is also possible to load the same module multiple times with different configurations. You can specify all the modules and the loading order in the Mesibo configuration file, `/etc/mesibo/mesibo.conf`. Mesibo will pass the data to each module in the order in which modules were loaded. 

![Module Chain](module_chain_process.jpg)

Creating a mesibo module is extremely easy. For example, you can implement a simple profanity filter module as follows.

### Building a profanity filter

- Implement a callback function to process all the incoming messages and pass it to mesibo - we will call it `on_message` function 
- When any user sends a message, Mesibo will invoke `on_message` callback function of your module with the message data, and its associated message parameters such as sender, expiry, flags, etc. 
- Your module can analyze the message to find any profanity or objectionable content and return whether the message is safe or not. 
- If no profanity is found, you can `PASS` the message and safely send it to the recipient; else you can `CONSUME` the unsafe message and prevent the message from reaching the receiver.

Now that you understand the basic functionality of a Mesibo module, let's dive deeper into the technical details of what forms a mesibo module.

## Anatomy of a Mesibo Module

A Mesibo module has three components:

 1. An initialization function which will be called once by Mesibo after loading the module
 2. A set of callback functions that will be defined by the module. These functions are called by Mesibo as and when required
 3. A module definition structure which declares above-mentioned functions and other configuration information
 
![Anatomy of Module](module_process_diagram.jpg)

## Module Definition
A mesibo module is described by `mesibo_module_t` structure as defined below. This is one of the most important data structures used in the Mesibo module. 

```cpp
typedef struct mesibo_module_s {
    mesibo_uint_t    version;     /* mesibo module API version */
    mesibo_uint_t    flags;        /* module flags */

    const char         *name;        /* module name */    
    const char         *description;    /* module description */    
    void        *ctx;        /* module context */
    module_config_t *config;    /* module configuration */

    mesibo_int_t    (*on_cleanup)(mesibo_module_t *mod);

    mesibo_int_t    (*on_message)(mesibo_module_t *mod, mesibo_message_params_t *params, const char *message, mesibo_uint_t len);
    mesibo_int_t    (*on_message_status)(mesibo_module_t *mod, mesibo_message_params_t *params, mesibo_uint_t  status);
    
    mesibo_int_t    (*on_call)(mesibo_module_t *mod);
    mesibo_int_t    (*on_call_status)(mesibo_module_t *mod);

    mesibo_int_t    (*on_login)(mesibo_module_t *mod, mesibo_user_t *user);

    mesibo_uint_t    signature;    /* module signature */

    uintptr_t    reserved_0;
    uintptr_t    reserved_1;
    uintptr_t    reserved_2;
    uintptr_t    reserved_3;
    uintptr_t    reserved_4;
    uintptr_t    reserved_5;
    uintptr_t    reserved_6;
    uintptr_t    reserved_7;
    
    //this function will be initialized by Mesibo
    mesibo_int_t    (*invoke)(mesibo_int_t id, mesibo_module_t *mod, ...);
} mesibo_module_t;

```

In the next section, we will learn how to initialize the module definition structure. Before that, it is essential to know how Mesibo knows about your module and loads it into the memory. 

### Loading a module
To load a module, you need to tell mesibo where to find your module, the directory path where your module shared library (`.so` file) is located, and the name of the module. You need to specify these details in the configuration file `/etc/mesibo/mesibo.conf`. You can also pass any other configuration that your module requires in the configuration files. 

By default, mesibo looks for the file `mesibo_mod_<module name>.so` in `/usr/lib64/mesibo/` directory. For example, if your module name is `chatbot`, mesibo will try to load the file `/usr/lib64/mesibo/mesibo_mod_chatbot.so`

Since mesibo on-premise runs in a docker container, you can keep modules in any directory and map the directory path by using the `-v` option when you run the Mesibo container. You also need to mount the directory `/etc/mesibo/` which contains your mesibo configuration file `mesibo.conf` where you need to specify the name of the module and configuration. For example, 

```bash
$ sudo docker run  -v /certs:/certs -v /usr/lib64/mesibo/:/usr/lib64/mesibo/ \
         -v /etc/mesibo:/etc/mesibo -net host -d mesibo/mesibo <app token> 
``` 

### Mesibo Configuration file

To load a module, you need to specify the module name in the configuration file **mesibo.conf**. For example,

```
 module <module name>
```
 
Each module can have its own configuration. You can specify the module configuration consisting of `name` and the corresponding `value`, as shown below.

```
module <module name> {
  <config name> = <config value>
  <config name> = <config value>
  .
  .
  .
}
```

For example, for a module named `skeleton`, you can provide the configuration details as follows.

```
module skeleton {
  file_name = xyz
  auth_key = abc
}

```


To load multiple modules and their respective configuration :

```
module <module_1 name> {
  /**Configuration for module-1 **/
  }

module <module_2 name> {
  /**Configuration for module-2 **/
  }
 .
 .
 .
module <module_N name> {
  /**Configuration for module-N **/
  }
  
```  

The configuration items that you specify in the module configuration file will be available during module initialization as key-value pairs.

Refer to the code example in the next section to see how configuration details are passed during the initialization. 

## Module initialization
Mesibo initializes each module by calling a module initialization function.  The naming convention for this function is `mesibo_module_<module name>_init`. For example, if your module name is `chatbot`, the name of your initialization function MUST be `mesibo_module_chatbot_init`.

Your module MUST define this function. If not, mesibo will not be able to load the module, and the error message will be shown during the mesibo initialization. The prototype for which can be found in the file [module.h](https://github.com/mesibo/onpremise-loadable-modules/tree/master/include/module.h). 


```cpp
int mesibo_module_<module name>_init(mesibo_int_t version, mesibo_module_t *mod, mesibo_int_t len);
```

The initialization function takes the following parameters:
1. `version` of type `mesibo_int_t`, Version of mesibo module. You must check that the `version` matches `MESIBO_MODULE_VERSION` to ensure that the on-premise mesibo server is using the same module version as the module.
2. `mod` of type `mesibo_module_t*`, pointer to mesibo module configuration structure. Mesibo allocates this structure and passes it to module. The module must initialize this structure with the callback function pointer and the module description. 
3. `len` of type `mesibo_uint_t`, size of module definition structure. You must check that the `len` matches `sizeof(mesibo_module_t)` to ensure that the on-premise mesibo server is using the same module definition structure as the module.

> Mesibo provides a macro `MESIBO_MODULE_SANITY_CHECK` which does all the above checks and more. You should use it as the first line in your initialization code. 

The configuration items that you specified in the [Module Configuration file](#module-configuration-file) is available as a list of items in the structure `module_config_t` where each item is a name-value pair of the type `module_config_item_t`

For example, for a module named `skeleton`, you may define configuration as below

```
module skeleton {
  file_name = xyz
  auth_key = abc
}
```

the initialization function looks like as follows:

```cpp
int mesibo_module_skeleton_init(mesibo_int_t version, mesibo_module_t *m, mesibo_uint_t len) {
    
    	MESIBO_MODULE_SANITY_CHECK(m, version. len);

        if(m->config) {
                mesibo_log(m, 1, "Following configuration item(s) were passed to the module\n");
                for(int i=0; i < m->config->count; i++) {
                        mesibo_log(m, 0, "module config item: name: %s value: %s\n", 
                        m->config->items[i].name, 
                        m->config->items[i].value);
                }
        }

        m->flags = 0;
        m->description = strdup("Sample Module");

	// initialize callback functions 
    	m->on_cleanup = skeleton_on_cleanup;
        m->on_message = skeleton_on_message;
        m->on_message_status = skeleton_on_message_status;
        m->on_login = skeleton_on_login;
        mesibo_log(m, 1, "================> %s init called\n", m->name);

        return MESIBO_RESULT_OK;

}
```

Above code will print the configuration details for the example module `skeleton` in logs:

```
Following configuration item(s) were passed to the module
module config item: name: file_name value: abc
module config item: name: auth_key value: xyz
```

## Module Callback Functions
There is a set of callback functions that need to be defined by the module. These callback functions are called by Mesibo based on different events. For example, 

- When users send a message
- When there is a status update for the messages sent by the module 
- When users make a call
- When users logs-in or logs-out
- When the module is unloaded, to reclaim memory/ perform the cleanup

The module needs to initialize the module definition structure with callback function pointers during the initialization, as shown in the above example. In above initialization example, `skeleton_cleanup`, `skeleton_on_message`, `skeleton_on_message_status`, `skeleton_on_login` are callback functions.

It is not mandatory to define all the callbacks. A module can define only those callbacks that the module is interested in. The function prototypes for all the callback functions are found in the file [module.h](https://github.com/mesibo/onpremise-loadable-modules/tree/master/include/module.h).

Let's look in detail at the different callback functions and their prototypes:

### on_message 
This function is called whenever a user sends a message. The module can process messages or can ignore as appropriate. The module can then indicate Mesibo whether to pass the message to the next module/recipient OR drop it by returning an appropriate value. 

```cpp
  mesibo_int_t (*on_message)(mesibo_module_t *mod,
                             mesibo_message_params_t *params,
                             const char *message, mesibo_uint_t len);
```

Parameters:
1. `mod`, pointer to mesibo module structure
2. `params`, pointer to message parameters structure. It contains message parameters such as `id`, `from`-  sender of the message, `to`-  message recipient, etc. For more details, refer [Module Data Structures](#module-data-structures).
3. `message`, buffer containing the message data bytes
4. `len`, length of the message in bytes

Returns:
- `MESIBO_RESULT_PASS` to pass the message data and parameters as it is to the next module or the recipient
- `MESIBO_RESULT_CONSUMED` message is consumed by the module and will not be passed to the next module or the recipient


### on_message_status
This function is called whenever there is a status update for the messages sent from the module

```cpp
mesibo_int_t (*on_message_status)(mesibo_module_t *mod,
                                    mesibo_message_params_t *params,
                                    mesibo_uint_t status);
```
Parameters:
1. `mod`, pointer to mesibo module structure
2. `params`, pointer to message parameters structure. It contains message parameters such as `id`, `from`-  sender of the message, `to`-  message recipient, etc. For more details, refer [Module Data Structures](#module-data-structures).
3. `status` containing the status of the sent message which corresponds to different [status codes](https://mesibo.com/documentation/api/real-time-api/data-structures/#messageparams) such as `MSGSTATUS_SENT`, `MSGSTATUS_DELIVERED`, `MSGSTATUS_READ`, etc

Returns:

`MESIBO_RESULT_OK` 

### on_call
```cpp
mesibo_int_t (*on_call)(mesibo_module_t *mod)
```

### on_call_status
```cpp
mesibo_int_t (*on_call_status)(mesibo_module_t *mod)
```

### on_login
This function is called whenever a user logs-in or logs-out. 

```cpp
mesibo_int_t (*on_login)(mesibo_module_t *mod, mesibo_user_t *user);
```
Parameters:
1. `mod`, pointer to mesibo module structure
2. `user`, pointer to mesibo user structure. Refer [Module Data Structures](#module-data-structures). Contains user parmeters flag, user address and online status.

Returns:

`MESIBO_RESULT_OK` 

### on_cleanup
This function is called when the module process is complete and to clean up.

```cpp
mesibo_int_t (*on_cleanup)(mesibo_module_t *mod)
```

##  Module Helper Functions
Mesibo provides various helper functions for tasks like [send a message](#mesibo_message), [send an HTTP request](#mesibo_http), [print logs](#mesibo_log), etc. 

### mesibo_message
This function can be used to send messages from the module to users and groups.

```cpp
  mesibo_int_t mesibo_message(mesibo_module_t *mod, mesibo_message_params_t *params,
                      const char *message, mesibo_uint_t len);
```
Parameters:
1. `mod`, pointer to mesibo module structure
2. `params`, pointer to message parameters structure. It contains message parameters such as `id`, `from`-  sender of the message, `to`-  message recipient, etc. For more details, refer [Module Data Structures](#module-data-structures).
3. `message`, buffer containing the message data bytes
4. `len`, length of the message in bytes

Returns:
Integer : 0 on success , -1 on failure

For example,

```cpp
mesibo_message_params_t p;
memset(&p, 0, sizeof(p));

p.to = 'user_source';
p.from = 'user_destination';
p.id = rand();

const char* message = "Hello from Module";

mesibo_uint_t len = strlen(message);
mesibo_message(mod, &p, message, len);
```

### mesibo_http
This function can be used to make an HTTP request. It is especially useful to invoke services like DialogFlow, etc.

```cpp
mesibo_int_t mesibo_http(mesibo_module_t *mod, const char *url, const char *post,
              mesibo_module_http_data_callback_t cb, void *cbdata,
              module_http_option_t *opt);
```

Parameters:
1. `mod`, pointer to mesibo module structure
2. `url`, both http and https URL are supported. You can also pass authentication information in URL. For example, https://username:password@yourapiurl.com
3. `post`, is a string that contains raw POST data. For example, "authtoken=xyz&user=abc" 
4. `cb`, is the call back function pointer whose prototype should match `mesibo_module_http_data_callback_t`. You will get the response of your http request, asynchronously through this callback function. Refer the example [HTTP Callback Function](#http-callback-function) provided.
5. `cbdata` is a pointer to data of arbitrary user-defined type. This callback data is passed on to the callback function that you have passed in the previous argument. You can store data of any arbitrary type such as a C/C++ struct and pass it as callback data to your call back function. For more details, refer to the [sample code]()
6. `opt` is the structure that contains `options` or additional parameters that you can pass in your HTTP request such as extra_header, content_type, etc. For more details about the `module_http_option_t` structure, refer [Module Data Structures](#module-data-structures).

Returns:
Integer : 0 on success , -1 on failure

For example,

```cpp
const char* url = "https://example.com/api.php"; //API endpoint
const char* post = "op=userdel&token=123434343xxxxxxxxx&uid=456"; // POST Request Data
mesibo_http(mod, url, post, mesibo_http_callback, NULL, NULL);
```

#### HTTP Callback Function
The callback function reference, `cb` that you pass as a parameter should be defined as per the function prototype `mesibo_module_http_data_callback_t` in `module.h`.

```cpp
typedef int (*mesibo_module_http_data_callback_t)(void *cbdata,
                                                  mesibo_int_t state,
                                                  mesibo_int_t progress,
                                                  const char *buffer,
                                                  mesibo_int_t size);
```

The callback function takes the following parameters:  
 1. `cbdata`, Pointer to arbitrary data, which the response callback function may need. You pass this while calling the request function [http](#http)
 2. `state`, An integer indicating the state of the response data being passed

```cpp
typedef enum {
  MODULE_HTTP_STATE_REQUEST,
  MODULE_HTTP_STATE_REQBODY,
  MODULE_HTTP_STATE_RESPHEADER,
  MODULE_HTTP_STATE_RESPBODY,
  MODULE_HTTP_STATE_DONE
} module_http_state_t;
```

 3. `progress`, progress from 0 to 100. Progress is negative (< 0) for error 
 4. `buffer`, response data 
 5. `size`, size of data in the buffer, in bytes

For example,

```cpp
static int mesibo_http_callback(void *cbdata, mesibo_int_t state,
        mesibo_int_t progress, const char *buffer,
        mesibo_int_t size) {

    tMessageContext *b = (tMessageContext *)cbdata;
    mesibo_module_t *mod = b->mod;

    if (progress < 0) {
        mesibo_log(mod, 0, " Error in http callback \n");
        
        // cleanup
        
        return -1;
    }

    if (state != MODULE_HTTP_STATE_RESPBODY) {
        return 0;
    }

    memcpy(b->buffer + b->datalen, buffer, size);
    b->datalen += size;

    if (progress == 100) {
        // process it ...
    }

    return 0;
}

```

### mesibo_log
This function can be used to print to mesibo container logs.

```cpp
mesibo_int_t mesibo_log(mesibo_module_t *mod, mesibo_uint_t level, const char *format,
        ...);

```

Parameters:
1. `mod`, Pointer to mesibo module structure
2. `level`, log level. The logs with level 0 will always be printed. 
If level > 0, logs will be printed only if the level is enabled in the configuration file
3. `format`, string for printing data which is similar to that of `printf` followed by the data to print.

Returns:
Integer : 0 on success , -1 on failure

For example,

```cpp
mesibo_log(mod, 0, "%s\n", "Hello, from Mesibo Module!");
```
### mesibo_util_getconfig
This function can be used to get the value of a configuration item from the name-value configuration list passed in `/etc/mesibo/mesibo.conf`

```cpp
char* mesibo_util_getconfig(mesibo_module_t* mod, const char* item_name);
```
Parameters:
1. `mod`, Pointer to mesibo module structure
2. `item_name`, Name of the configuration item

Returns:
String: Configuration item value

For example,
If the configuration is provided as 
```
module = skeleton {
file = abc
auth_token = xyz
}
```
Then, 

```cpp
char* item_val =  mesibo_util_getconfig(mod, "file");
```
`item_val` will contain the string `abc`.

### mesibo_util_json_extract
This function can be used to get the value of a key in a JSON string.

```cpp
 char* mesibo_util_json_extract(char *src, const char *key, char **next);                                                                           
```
Parameters:
1. `src`, JSON string source
2. `key`, Key string
3. `next`, Pointer to a list of json strings (If you need to provide a list of JSON strings)

Returns: String: Value Matching the Key in the JSON string.

For example,

```cpp
char* test_json = strdup("{ \"a\":\"apple\", \"b\" : \"ball\" }");
char* value = mesibo_util_json_extract(test_json, "a" , NULL);
```
`value` will contain the string `apple`

## Module Data Structures

### Message Parameters Structure
The C/C++ structure `mesibo_message_params_t` is used to define the various parameters of an incoming or an outgoing message. Message params is used as argument to functions such as `on_message`, `on_message_status`, `send_message`, etc.

```cpp
typedef struct mesibo_message_params_s {
    mesibo_uint_t aid;
    mesibo_uint_t id;  
    mesibo_uint_t refid;
    mesibo_uint_t groupid;
    mesibo_uint_t flags;
    mesibo_uint_t type;
    mesibo_uint_t expiry;
    mesibo_uint_t to_online;

    char *to, *from;
} mesibo_message_params_t;
```

- `aid` - Application ID.
- `id`  - ID of the incoming message. For outgoing messages, id should be specified in `send_message` function.
- `refid` - Reference id (id of another message) to which the current message can be linked against.
- `groupid` - Group ID should be specified when sending a message to a group, 0 for one-to-one messages. 
- `flags`- Message Flags
- `type` - Message Type, any arbitrary user-defined types
- `expiry` - Message Expiry for an outgoing message (time to live), in seconds
- `to_online` - Send the message only if the recipient is online 

### Mesibo User

```cpp
typedef struct  mesibo_user_s {
    mesibo_uint_t flags;
    char *address;
    mesibo_int_t online;

}mesibo_user_t;
```
- `flags` - User flags
- `address`- User Address. Can be any sequence of characters that identifies a user
- `online` - Online Status of user

### HTTP Options Structure
To pass`options` parameter of a HTTP request in the function [mesibo_http()](#mesibo_http) you use the C/C++ structure `module_http_option_t`

```cpp
typedef struct _module_http_option_t {
    const char *proxy;

    // body or post data
    const char *content_type;  // body content type

    const char *extra_header;
    const char *user_agent;
    const char *referrer;
    const char *origin;
    const char *cookie;
    const char *encoding;  // could be gzip, deflate, identity, br (do not use
    // 'compress' which is obsolete)
    const char *cache_control;  // cache control and expiry
    const char *accept;
    const char *etag;
    mesibo_uint_t ims;  // if modified since, gmt time

    mesibo_uint_t maxredirects;

    mesibo_uint_t conn_timeout, header_timeout, body_timeout, total_timeout;

    mesibo_uint_t retries;

} module_http_option_t;
```

### Options Fields  

- `proxy` Proxy URL, if any.
- `content_type` Content-Type header. For example "application/json".
- `extra_header` Any custom headers you like to send, such as contain Authorisation header, etc
- `user_agent` User Agent, default mesibo/x.x
- `referrer` HTTP referer header
- `origin` HTTP origin header
- `cookie` Send HTTP Cookie Header
- `encoding` HTTP content encoding header
- `cache_control` HTTP content encoding header
- `accept`
- `etag`
- `ims` Set If-Modified-SInce header, timestamp
- `maxredirects` 
- `conn_timeout`, `header_timeout`, `body_timeout`, `total_timeout` are Settable Timeouts for every state of the protocol (connection, headers, body)
- `retries` Retry broken downloads and uploads

### Module Configuration Structure
The configuration attributes for a module can be provided as a configuration list which shall be made available in the mesibo module initialization function, through the following structures

```cpp
typedef struct module_config_item_s {
    char *name;
    char *value;
} module_config_item_t;
```

```cpp
typedef struct module_configs_s {
    int count;
    module_config_item_t items[0];
} module_config_t;
```

`module_config_t` contains `count`- the number of items in the configuration list &  a list of items of type  `module_config_item_t` - a structure containing a name-value pair.

## Memory Management
Since Mesibo is capable of handling millions of messages and users, your module callback functions can be invoked millions of times. Hence, you should avoid allocating memory dynamically. Instead, you should try to allocate required memory and complex operations during initialization.

#### Module Definition Structures
Module definition structure is allocated by Mesibo, and you MUST not free it.

#### Config Items Structures
Configuration items structure is allocated by Mesibo and can be freed if required. However, configuration key-value pairs are statically allocated. You can freely use those pointers but should never free them.

## Writing and Compiling Mesibo Modules
We have published source code of various useful modules like a chatbot, translation, filter, etc. on [GitHub](https://github.com/mesibo/onpremise-loadable-modules). It is recommended that you start by downloading the repository.

The repository also contains the source code of a skeleton module and other necessary files like a module header file and a Makefile to compile a base module quickly. You can then modify it to suit your needs.

### Downloading the Sample Modules Repository

#### Clone the Repository (Recommended)
If you have git installed, this is a recommended approach as you can quickly sync and stay up to date with the latest version. This is also a preferred way of downloading the code if you decide to contribute to the project. 

To download, open a terminal and issue following commands:

    $ mkdir mesibo-modules
    $ cd mesibo-modules
    $ git clone https://github.com/mesibo/onpremise-loadable-modules.git

#### Download the code as a zip file
You can also download the complete modules repository as a zip file. Although simple, the downside of this approach is that you will have to download the full source code every time it is updated on the repository. 

Click on the `Download` button to start downloading.

[Download](https://github.com/mesibo/onpremise-loadable-modules/archive/master.zip){: class="button outline-btn" style="margin-bottom: 10px; margin-right: 100%"}

Once the download completes, 

1. Compile all the sample modules to ensure that modules compile successfully on your machine. 
1. Make a copy of `skeleton` module 
3. Modify `skeleton` module by changing the module name, callback functions, etc. For a detailed example, refer to the [Building a chat-bot](#building-a-chat-bot) below
4. Edit `MakeFile` and modify the `MODULE` field to reflect module name in the output file correctly

```cmake
#Sample MakeFile to build mesibo module 
MODULE=skeleton

include ../make.inc/make.inc
```

## Code references and Examples

### Building a chat-bot 
Now since we have learned about how the module works, we will build a simple but capable chatbot, which can integrate powerful analytical abilities in speech, image recognition, Natural Language processing, etc. in your backend using loadable modules. You can interface with any tool or library of your choice, such as Dialogflow, IBM Watson, Tensorflow, etc.

![Module Chatbot Sample](module_chat_bot.jpg)

Let's look at how you can build a chatbot using mesibo modules:

- Create a dedicated user (destination) for the chatbot, so that when a user sends a message to this destination, chatbot functionality can be invoked.
- When a user sends a message, your module will get the message text via the callback function `on_message` along with message parameters. 
- Check `'to'` field (destination) in message parameters to check if it was sent to the chatbot. If it's not the chatbot destination, return PASS as explained in `on_message` callback function description so that the message can be sent to the requested destination.
- Send the message to your message processing model which could be a local function or a remote service like Dialogflow, IBM Watson, Tensorflow, etc. Your messaging processing model will process the message and send the appropriate response.
- You can send the response back to the user (sender) using `mesibo_message` function.

Refer to the [Sample Chatbot Module](https://github.com/mesibo/onpremise-loadable-modules/tree/master/chatbot) source code which demonstrates building your module with a Dialogflow chatbot. 

For this chatbot example, We will be using DiaglogFlow to process messages. Hence, before we dive into the code, let us understand how Dialogflow works. You can skip this section if you are already familiar with Dialogflow. 

### Basics of Dialogflow
Dialogflow is an AI powered Google service to build interactive conversational interfaces, such as chatbots. Once you train DialogFlow using data of your interest like emails, FAQs, etc., it can answer questions from your users in natural language.

Dialogflow service is available through a REST interface. For more details on using Dialogflow , refer [DialogFlow Documentation](https://cloud.google.com/dialogflow/docs/quick/api)

#### Configuring Dialogflow API 
To use Dialogflow API, you will need two parameters which you can obtain from the Google Cloud Console.

  - GCP project ID
  - Access Token

Following are the the steps:

1. Set up a [GCP Console](https://console.cloud.google.com) project.
    - Create or select a project and note the project ID
    - Create a service account
    - Download a private service account key as JSON

2. Set the environment variable `GOOGLE_APPLICATION_CREDENTIALS` pointing to the JSON file downloaded in the Step 1.

```
export GOOGLE_APPLICATION_CREDENTIALS="/home/user/Downloads/service-account-file.json"
```

3. [Install and initialize the Cloud SDK](https://cloud.google.com/sdk/docs/)
4. Print your access token by using the following command

```
echo $(gcloud auth application-default print-access-token)
```

which should output something like

```
ya29.c.Kl6iB-r90Gjj4o--m7k7wr4dN4b84U4TLEtPqdEZ2xvfsj01awmUObMDEFwJIJ1lkZPym5dsAw44MbZDSaksLH3xKbsSHGLgWeEXqIPSDmFO6
```
This is the access token, save it for later use.

#### Invoking Dialogflow API  
Once we have project ID and access token, invoking DialogFlow API is as simple as invoking following URL with access token and the data:

```
https://dialogflow.googleapis.com/v2/projects/<Project ID>/agent/sessions/<Session ID>
```

Where, Project ID is the GCP Project ID obtained earlier. Session ID can be a random number or some type of user and session identifiers (preferably hashed). 

For example, a sample dialogflow REST URL looks like

```
https://dialogflow.googleapis.com/v2/mesibo-dialogflow/agent/sessions/123456789
```

Now, you send a POST request to the above URL in the following format.

Pass the authentication information in the request header.

```
Authorization: Bearer <YOUR_ACCESS_TOKEN>
Content-Type: application/json
```

and the POST data which contains your text/message.

```
{
    "queryInput": {
 	{
	"text": {"text": <message text>}
	}   
    }
}
```

That's it! Now since we know how to use mesibo-modules and the DialogFlow REST API, following is a step-by-step tutorial for building a chat-bot using mesibo module with Dialogflow:

### 1. Create The C/C++ Source file

Since we will be building a chatbot , let our module name be `chatbot`. Create a C/C++ Source file named `chatbot.c` and 
include the header file `module.h` in your code.

```cpp
#include "module.h"
```

### 2. Configuration for the chatbot module
The chatbot module uses DialogFlow. It would be nice if we can define some configuration entities to configure chatbot parameters and DialogFlow parameters. 

We will define the following configurable items for The chatbot module:

```
module chatbot{
project = <project name>
session = <session id>
access_token = <service account key>
endpoint = <dialogflow service endpoint>
address = <chatbot user address>
log = <log level>
}
```

where, 
- `project`, GCP Project ID that contains your dialogflow chatbot.
- `session`, The Session ID for the queries being sent . 
- `endpoint`, The dialogflow REST endpoint to which your query will be sent
- `access_token`, access token linked with your project. In this example we will be passing dialogflow client access token in the auth header like so `Authorisation: Bearer <access token>.`
- `address`, chatbot address. In your Mesibo Application, create a user that you can refer to as a chatbot endpoint user. 
- `log`, Log level for printing to mesibo container logs

For example,

```
module chatbot{
project = mesibo-chatbot
session = 4e72c746-7a38-66b6-xxxxxx
endpoint = https://dialogflow.googleapis.com/v2
access_token = xxxxxx.Kl6iBzVH7dvV2XywzpnehLJwczdClfMoAHHOeTVNFkmTjqVX7VagKHH1-xxxxxxx
address = my_chatbot
log = 1
}
```

### 3. Initialize the module
Now, we need to provide the initialization function for our module. Since we chose the name for the module as `chatbot`, the name of the initialization function will be `mesibo_module_chatbot_init`, as defined below.

```cpp
int mesibo_module_chatbot_init(mesibo_module_t *m, mesibo_uint_t len) {
    
	MESIBO_MODULE_SANITY_CHECK(m, version. len);
    
	m->flags = 0;
	m->description = strdup("Sample Chatbot Module");
	m->on_message = chatbot_on_message;
    
	//Read configuration
	if(m->config) {
		chatbot_config_t* cbc = get_config_dialogflow(m);
                if(cbc  == NULL){
                        mesibo_log(m, MODULE_LOG_LEVEL_0VERRIDE, "%s : Missing Configuration\n", m->name);
                        return MESIBO_RESULT_FAIL;
                }
                m->ctx = (void* )cbc;

                int init_status = chatbot_init_dialogflow(m);
                if(init_status != MESIBO_RESULT_OK){
                        mesibo_log(m, MODULE_LOG_LEVEL_0VERRIDE, "%s : Bad Configuration\n", m->name);
                        return MESIBO_RESULT_FAIL;
                }
        }
        else {
                return MESIBO_RESULT_FAIL;
        }

    	return MESIBO_RESULT_OK;
}
```

We first check the sanity using `MESIBO_MODULE_SANITY_CHECK` and then retrieve the configuration and store it in the module context `m->ctx` so that it is available whenever module callbacks are invoked.

We will use the following configuration structure `chatbot_config_t` to store the configuration:

```cpp
typedef struct chatbot_config_s {
        /* To be configured in module configuration file */
        const char* project;
        const char* session;
        const char* endpoint;
        const char* access_token;
        const char* address;
        int log;

        /* To be configured by dialogflow init function */
        char* post_url;
        char* auth_bearer;
        module_http_option_t* chatbot_http_opt;

} chatbot_config_t;

```

A helper function, `mesibo_util_getconfig` can be used to get the configuation information, as shown below:

```cpp
static chatbot_config_t*  get_config_dialogflow(mesibo_module_t* mod){
        chatbot_config_t* cbc = (chatbot_config_t*)calloc(1, sizeof(chatbot_config_t));
        cbc->project = mesibo_util_getconfig(mod, "project");
        cbc->session = mesibo_util_getconfig(mod, "session");
        cbc->endpoint = mesibo_util_getconfig(mod, "endpoint");
        cbc->access_token = mesibo_util_getconfig(mod, "access_token");
        cbc->address = mesibo_util_getconfig(mod, "address");
        cbc->log = atoi(mesibo_util_getconfig(mod, "log"));

        return cbc;
}

```

#### Initialize REST API parameters
Once we obtain the configuration, we can construct REST API parameters (URL and header) so that we can use it when required, rather than constructing them at runtime. 

```cpp
static int chatbot_init_dialogflow(mesibo_module_t* mod){
        chatbot_config_t* cbc = (chatbot_config_t*)mod->ctx;

        asprintf(&cbc->post_url, "%s/projects/%s/agent/sessions/%s:detectIntent",
                        cbc->endpoint, cbc->project, cbc->session);
        asprintf(&cbc->auth_bearer, "Authorization: Bearer %s", cbc->access_token);
        
	cbc->chatbot_http_opt = (module_http_option_t *)calloc(1, sizeof(module_http_option_t));
        cbc->chatbot_http_opt->extra_header = cbc->auth_bearer;
        cbc->chatbot_http_opt->content_type = "application/json";

	return MESIBO_RESULT_OK;
}
```

### 3.`chatbot_on_message`
We only need to process messages addressed to the configured `address` of the chatbot. For all other messages, we PASS the message as it is.

```cpp
static mesibo_int_t chatbot_on_message(mesibo_module_t *mod, mesibo_message_params_t *p,
                const char *message, mesibo_uint_t len) {

        chatbot_config_t* cbc = (chatbot_config_t*)mod->ctx;
        
	if(0 == strcmp(p->to, cbc->address)){
                // Don't modify original as other module will use it
                mesibo_message_params_t* np = (mesibo_message_params_t*)calloc(1, sizeof(mesibo_message_params_t));
                memcpy(np, p, sizeof(mesibo_message_params_t));
                chatbot_process_message(mod, np, message, len);

                return MESIBO_RESULT_CONSUMED;  // Process the message and CONSUME original
        }
        
	return MESIBO_RESULT_PASS;
}
```

### 4. Processing the incoming message using DialogFlow
To process the incoming messages, we need to send them to DialogFlow and send the response back to the user.

To invoke Dialogflow API, we will be using the helper function `mesibo_http`. DialogFlow expects the request data in JSON format. Ideally, we could have used a JSON library to encode the request.  However, JSON libraries are typically slow and are an overkill for this simple project. Hence, we directly construct the raw post data string.

Once the response is received from DialogFlow, we need to send it to to the user who made the request. Hence, we store the context of the received message ie; message parameters, the sender of the message, the receiver of the message in the following structure and pass it as callback data in the http request. 

```cpp
typedef struct message_context_s {
        mesibo_module_t *mod;
        mesibo_message_params_t *params;
        char *from;
        char *to;
        // To copy data in response
        char buffer[HTTP_BUFFER_LEN];
        int datalen;

        char* post_data; //For cleanup after HTTP request is complete   
} message_context_t;
```

The function to process the message and send an HTTP request to Dialogflow is as follows:

```cpp
static int chatbot_process_message(mesibo_module_t *mod, mesibo_message_params_t *p,
                const char *message, mesibo_uint_t len) {

        chatbot_config_t* cbc = (chatbot_config_t*)mod->ctx;

        const char* post_url = cbc->post_url;
        char* raw_post_data; 
        asprintf(&raw_post_data, "{\"queryInput\":{\"text\":{\"text\":\" %.*s \"}}}",
                        (int)len, message);

        message_context_t *message_context =
                (message_context_t *)calloc(1, sizeof(message_context_t));
        message_context->mod = mod;
        message_context->params = p;
        message_context->from = strdup(p->from);
        message_context->to = strdup(p->to);

        mesibo_http(mod, post_url, raw_post_data, chatbot_http_callback,
                        (void *)message_context, cbc->chatbot_http_opt);
                        
        return MESIBO_RESULT_OK;
}
```  

### 5. Define the Callback function to receive the response from your bot

We will get the response for the POST request in the HTTP callback function passed to `mesibo_http`. You may get the response in multiple chunks. Hence you need to store the response data into a buffer untill the complete response is received. 

Dialogflow sends the response as a JSON string with the response text encoded in the field `fulfillmentText`. Hence, we first extract the response from the JSON string before we can send it back to the user. We will use a helper functon `mesibo_util_json_extract` to extract textual response from the JSON response.

You can pass the message-id of the query message as reference-id for the response message. This way the client who sent the message will be able to match the response received with the query sent. 

```cpp
static int chatbot_http_callback(void *cbdata, mesibo_int_t state,
                mesibo_int_t progress, const char *buffer,
                mesibo_int_t size) {
        message_context_t *b = (message_context_t *)cbdata;
        mesibo_module_t *mod = b->mod;
        chatbot_config_t* cbc = (chatbot_config_t*)mod->ctx;

        mesibo_message_params_t *params = b->params;

        if (progress < 0) {
                mesibo_chatbot_destroy_message_context(b);
                return MESIBO_RESULT_FAIL;
        }

        if (state != MODULE_HTTP_STATE_RESPBODY) {
                return MESIBO_RESULT_OK;
        }

        if ((progress > 0) && (state == MODULE_HTTP_STATE_RESPBODY)) {
                memcpy(b->buffer + b->datalen, buffer, size);
                b->datalen += size;
        }

	if (progress == 100) {

                char* extracted_response = mesibo_util_json_extract(b->buffer , "fulfillmentText", NULL);

		mesibo_message_params_t p;
                memset(&p, 0, sizeof(mesibo_message_params_t));
                p.id = rand();
                p.refid = params->id;
                p.aid = params->aid;
                p.from = params->to;
                p.to = params->from; // User adress who sent the query is the recipient
                p.expiry = 3600;

		mesibo_message(mod, &p, extracted_response , strlen(extracted_response));

                mesibo_chatbot_destroy_message_context(b);
        }

        return MESIBO_RESULT_OK;
}

```

### 6. Compile the chatbot module
To compile your module, open the sample `MakeFile` provided. Change the `MODULE` to `chatbot`. 

For example.

```
MODULE = chatbot
```

Run `make` from your source directory. 

```
sudo make
```

On successful build of your module, verify that the target path contains the shared library `/usr/lib64/mesibo/mesibo_mod_chatbot.so`

### 7. Load the chatbot module
To load your chatbot module provide the configuration in `/etc/mesibo/mesibo.conf`. You can copy the configuration from `sample.conf` into `/etc/mesibo/mesibo.conf`and modify values accordingly. Change the value of `project` to match the Google Project you are using. Change the value of [`access_token`](#getting-access-token) to your `SERVICE_ACCOUNT_KEY`.  

Mount the directory containing your library which in this case is `/usr/lib64/mesibo/`, while running the mesibo container
as follows. You also need to mount the directory containing the mesibo configuration file which in this case is `/etc/mesibo`

```
sudo docker run  -v /certs:/certs -v  /usr/lib64/mesibo/:/usr/lib64/mesibo/ \
         -v /etc/mesibo:/etc/mesibo -net host -d mesibo/mesibo <app token> 
```
