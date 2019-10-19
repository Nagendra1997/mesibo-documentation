---
description: Mesibo Loadable Modules
keywords: open, source, on-premise, messaging, chat, voice, video
title: Mesibo Loadable Modules
---

Mesibo is designed by developers for developers. Its dynamically loadable module architecture lets you add new features and your own custom functionalities for deeper integration with your infrastructure, enabling you for unlimited creative possibilities. 

With Mesibo Modules you can build powerful chatbots, filters, remotely communicate with hardware for IoT and robotics, integrate with Machine learning and Scientific computing backend such as Tensorflow, Dialogflow, Matlab, etc and much more, all the way keeping your data secure and private in your premises or private cloud.  

This makes Mesibo, the most compelling real-time communication platform existing today. In this document, we will describe how you can build and use Mesibo Modules to unlock new possibilities and innovative solutions.

{% comment %} 
## Overview
0. [Prerequisites](#prerequisites)
1. [Overview of a Mesibo Module](#what-is-a-mesibo-module)   
a. [What is a Mesibo Module ](#what-is-a-mesibo-module)   
b. [How do Mesibo Modules work](#how-do-mesibo-modules-work)  
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

## What is a Mesibo Module?

Mesibo Module is essentially a message processor which allows you to intercept each message and decide whether to pass the message as it is to the destination, drop it, or process it before sending it to the destination. For example,

- A **profinity filter module** can drop messages containing profanity 
- A **tranlator module** can translate each message before sending it to destination
- A **chatbot module** can analyze messages using various AI and machine learning tools like Tensorflow, Dialogflow, etc. and send an automatic reply. 


The functionality of each module is programmed by you and its capability is limited only by your imagination.  Mesibo modules make Mesibo a powerful communication platform.  


You can build a Mesibo Module on top of the core platform, as a shared library(basically a .so file) and load it to extend the functionality of the Mesibo.
![Module Architecture](Mesibo_Loadable_Modules.jpg)

### How do Mesibo Modules work?
A Mesibo module is a shared library (`.so` file) which can be loaded at runtime by the Mesibo server. Mesibo then invokes various callback functions that you have defined in the share library whenever it receives messages or message status from your users. Your module can then decide what to do with those messages, for example:

 - Pass the message as it is
 - Drop it
 - Process further before sending it to the destination
 - Reply to the sender
   
![Module Flowchart](module_flow_chart.jpg)

You can load multiple modules, each having their own features and functionalities. You can specify all the modules to be loaded and the order in the Mesibo configuration file, `/etc/mesibo/mesibo.conf`. Mesibo will pass data to each module in the order in which modules were loaded. 

![Module Chain](module_chain_process.jpg)

Creating a mesibo module is extremely easy.For example, you can implement a simple profanity filter module as follows.

### Building a profanity filter

- Implement a callback function to process all the incoming messages and pass it to mesibo - we will call it `on_message` function 
- When any user sends a message, Mesibo will invoke `on_message` callback function of your module with the message data and it's associated message parameters such as sender, expiry, flags, etc. 
- Your module can analyze the message to find any profanity or objectionable content and return whether the message is safe  or not. 
- If no profanity was found, you can `PASS` the message and safely send it to the recipient, else  you can `CONSUME` the unsafe message and prevent the message from reaching the receiver.

Now that you understand the overall functionality of a Mesibo module, let's dive deep into the technical details of what forms a mesibo module.

## Anatomy of a Mesibo Module

A Mesibo module has three parts:

- An initialization function which will be called by Mesibo once to initialize the module
- A set of callback functions that will be defined by the modules. These functions are called by Mesibo as and when required
- A set of callable functions provided by the Mesibo that can be called by the module as required
- A configuration structure which declares aboved mentioned functions and other configuration information
 ![Anatomy of Module](module_process_diagram.jpg)

### Mesibo Module Configuration 
A Mesibo Module is described by `mesibo_module_t` structure as defined below. This is one of the most important data structures used in the Mesibo module. 

```cpp
typedef struct mesibo_module_s {
  mesibo_uint_t version; /* mesibo module API version */
  mesibo_uint_t flags;   /* module flags */

  const char *name; /* module name */
  void *ctx;        /* module context */

  mesibo_int_t (*cleanup)(mesibo_module_t *mod);

  mesibo_int_t (*on_message)(mesibo_module_t *mod,
                             mesibo_message_params_t *params,
                             const char *message, mesibo_uint_t len);
  mesibo_int_t (*on_message_status)(mesibo_module_t *mod,
                                    mesibo_message_params_t *params,
                                    mesibo_uint_t status);

  mesibo_int_t (*on_call)(mesibo_module_t *mod);
  mesibo_int_t (*on_call_status)(mesibo_module_t *mod);

  mesibo_int_t (*on_login)(mesibo_module_t *mod, mesibo_login_params_t *params);

  uintptr_t reserved_0;
  uintptr_t reserved_1;
  uintptr_t reserved_2;
  uintptr_t reserved_3;

  mesibo_uint_t signature; /* module signature */

  uintptr_t reserved_4;
  uintptr_t reserved_5;
  uintptr_t reserved_6;
  uintptr_t reserved_7;

  // These functions will be initialized by Mesibo
  mesibo_int_t (*send_message)(mesibo_module_t *mod,
                               mesibo_message_params_t *params,
                               const char *message, mesibo_uint_t len);
  mesibo_int_t (*http)(mesibo_module_t *mod, const char *url, const char *post,
                       mesibo_module_http_data_callback_t cb, void *cbdata,
                       module_http_option_t *opt);
  mesibo_int_t (*log)(mesibo_module_t *mod, mesibo_uint_t level,
                      const char *format, ...);
} mesibo_module_t;

```

In the next section, we will learn how to initialize module configuration structure. Before that, it is essential to know how Mesibo knows about your modules and loads it into the memory. 

### Loading a module
As described previously, a mesibo module is a shared library (`.so` file) that is loaded at runtime by the Mesibo. For Mesibo to load your module, you need to tell mesibo about the module you wish to load for example, path where your module is located and the name of the module shared library.

For example, the path to your module could be `/usr/lib64/mesibo/mesibo_test.so`
and the name of your module could be `test` with `test.cpp` being your C/C++ source file.

You need to specify these details in the configuration file `/etc/mesibo/mesibo.conf`. You can also pass any other details that your module requires as a list of name-value pairs,where the `name` will correspond to the configuration attribute and the `value` being the defined value for that attribute.  

### Mesibo Configuration file

To load a module, you need to specify the <module name> in the configuration file **mesibo.conf** like so:
```
 module = <module name>
```
 
You can specify module configuration details, as a configuration list consisting of `attribute name` and the corresponding `attribute value` as shown below.

```
module = <module name> {
  <attribute name> = <attribute value>
  <attribute name> = <attribute value>
  .
  .
  .
}
```
For example, for a module named `test` you can provide the configuration details as follows.
```
module = test {
  file_name = xyz
  auth_key = abc
}

```


To load multiple modules and their respective configuration :
```
module = <module_1 name> {
  /**Configuration for module-1 **/
  }

module = <module_2 name> {
  /**Configuration for module-2 **/
  }

module = <module_3 name> {
  /**Configuration for module-3 **/
  }
  
 .
 .
 .
 module = <module_N name> {
  /**Configuration for module-N **/
  }
  
```  

The config items that you specify in the module configuration file, will be available during module initialisation,as an argument to the initialization function `mesibo_module_<module name>_init` which is of type [module_configs_t](#module-configuration-structure)

Refer to the code example in the next section, to see how configuration details are passed during initialisation. 

### Module initialization
The above module structure initialization is performed by a callback function, the prototype for which can be found in the file [module.h](). This function is automatically called by mesibo when the module is constructed. The naming convention for this function is `mesibo_module_<module name>_init`.

The function takes the following parameters:
1. `version` of type `mesibo_int_t` Version of Mesibo Module
2. `mod` of type `mesibo_module_t*` Pointer to mesibo module struct
3. `len` of type `mesibo_uint_t` Size of configuration Structure
4. `config` of type ` module_configs_t *` Pointer to mesibo module configuration struct

It is important to note that the size of the module structure `mesibo_module_t` defined should be equal to `len` and `signature` of your module should match with the defined `MESIBO_MODULE_SIGNATURE`.  You must check the module structure length,version and singature to ensure that structure is aligned as expected. 

The configuration list that you pass in the [Module Configuration file][#module-configuration-file] is available as a list of items in the structure `module_configs_t` where each item is a name-value pair of the type `module_config_item_t`

For example,for a module named `test` (** which is defined in a file named `test.cpp`) and the configuration provided is as below
```
module = test {
  file_name = xyz
  auth_key = abc
}
```
the initialisation function looks like the following:
```cpp
int mesibo_module_test_init(mesibo_int_t version, mesibo_module_t *m, mesibo_uint_t len, module_configs_t *config) {
        if(MESIBO_MODULE_VERSION != version) {
                m->log(m, 0, "module version mismatch\n");
                return -1;
        }

        if(sizeof(mesibo_module_t) != len) {
                m->log(m, 0, "module size mismatch\n");
                return -1;
        }
        
        if(MESIBO_MODULE_SIGNATURE != m->signature) {
                m->log(m, 0, "module signature mismatch\n");
                return -1;
        }

        if(config) {
                m->log(m, 0, "Following configuration item(s) were passed to the module\n");
                for(int i=0; i < config->count; i++) {
                        m->log(m, 0, "module config item: name %s value %s\n", 
                        config->items[i].name, 
                        config->items[i].value);
                }
        }

        m->version = 1;
        m->flags = 0;
        m->name = strdup("Sample Module");
        m->cleanup = test_cleanup;
        m->on_message = test_on_message;
        m->on_message_status = test_on_message_status;
        m->log(m, 0, "================> %s init called\n", m->name);
                                                                     

  return 0;
}
```
The logs will print the the configuration details for the example module `test`:
```
Following configuration item(s) were passed to the module
module config item: name file_name value abc
module config item: name auth_key value xyz
```
`test_cleanup`, `test_on_message`, `test_on_message_status` are functions that have been defined by you and serve as the corresponding callback function references to cleanup,on_message & on_message_status .

### b. Callback functions
There are a set of callback functions that need to be initialized by the caller of the module. These callback functions are called by Mesibo based on different events. Callback functions that you provide should have the same signature as defined in the `mesibo_module_t` struct.
For a detailed explanation of callback functions and their prototypes refer [Callback functions](#callback-functions)

`on_message`: When a message is sent from one endpoint to another in your application 

`on_messagestatus`: When a message is sent and you will receive a status update

`on_call`: When a call is received

`on_call_status`: When a call status is received

`cleanup` When module completes its work and is unloaded


### c. Callable functions
Some functions are initialized by Mesibo which you can use. For a detailed explanation refer [Callable functions](#callable-functions)

`send_message` To send message from one user to another

`http` To send HTTP ie; REST type request

`log` To print logs to mesibo server logs


### Callback Functions
There are different callback functions that you can define in your module and instruct mesibo to call these functions under different events such as : 
- When you get a message
- When you send a message and get status of the sent message (Sent, Received or delivered)
- When you receive a call
- When you complete the module process and reclaim memory/ perform the cleanup

You need to provide the callback functions with the same prototype while initializing the module: ie; same number of arguments, data types of arguments and return type. All function prototypes are found in the file [module.h]().

### Pass and consume mechanism
Pass or Consume mechanism is followed according to return value of the function: Each function returns an integer value which is defined in the file [module.h]()     
`MESIBO_RESULT_PASS` pass the data as it is and the recipient is notified  
`MESIBO_RESULT_CONSUMED` where the data is consumed and the recipient is not notified of this data


Let's look in detail at the different callback functions and their prototypes:

### on_message 
This function is called when the module receives a message.
```cpp
  mesibo_int_t (*on_message)(mesibo_module_t *mod,
                             mesibo_message_params_t *params,
                             const char *message, mesibo_uint_t len);
```
Parameters:
1. `mod` Pointer to mesibo module struct
2. `params` Pointer to message params struct. It contains message parameters such as 
`id`- Unique message identifier(For example it can be a pseudo-random number), `from`-  sender of the message, `to`-  message recipient, etc For more details refer [Data Structures](#data-structures).
3. `message` character buffer  which contains the message data bytes
4. `len` contains the length of the message ie; number of bytes in the data buffer

Returns:

`MESIBO_RESULT_CONSUMED` if message data is to be consumed and the recipient will not receive THIS message data.

`MESIBO_RESULT_PASS` pass the message data and parameters as it is to the recipient

### on_message_status
This function is called when a message is sent from the module and you receive the status of the message.
```cpp
mesibo_int_t (*on_message_status)(mesibo_module_t *mod,
                                    mesibo_message_params_t *params,
                                    mesibo_uint_t status);
```
Parameters:
1. `mod` Pointer to mesibo module struct
2. `params` Pointer to message params struct. It contains message parameters such as 
`id`- Unique message identifier(For example it can be a pseudo-random number), `from`-  sender of the message, `to`-  message recipient, etc For more details refer [Data Structures](#data-structures).
3. `status` containing the status of the sent message which corresponds to different [status codes](https://mesibo.com/documentation/api/real-time-api/data-structures/#messageparams) such as `MSGSTATUS_SENT`,`MSGSTATUS_DELIVERED`,`MSGSTATUS_READ`, etc

Returns:

`MESIBO_RESULT_CONSUMED` if message data is to be consumed and the recipient will not be notified of THIS status data.

`MESIBO_RESULT_PASS` pass the status data and parameters as it is to the recipient

### on_cleanup
This function is called when the module process is complete and to clean up.
```cpp
mesibo_int_t (*cleanup)(mesibo_module_t *mod)
```
### on_call
```cpp
mesibo_int_t (*on_call)(mesibo_module_t *mod)
```
### on_call_status
```cpp
mesibo_int_t (*on_call_status)(mesibo_module_t *mod)
```
### on_login
This function is called when a user logs in. When the user logs in, your server logs will conntain an entry saying X user logged in and here are the user-login parameters. The function callback you define,will get arguments passed containing login paramters such as user flags, online status, user address,etc. For more details refer to the data structure info on  [mesibo_user_t](#mesibo-user)
```cpp
mesibo_int_t (*on_login)(mesibo_module_t *mod, mesibo_user_t *user);
```

##  Callable Functions
These functions are initialized by Mesibo and you can utilize these functions to [send a message](#send_message), [send an HTTP request](#http), [print logs](#log), etc. Please note, that unlike the callback functions you need not pass any function references to initialize these functions as they are internally defined and initialized by Mesibo.

Let's look at the syntax of these functions.

### send_message
This function can be used to send a message from one end-point/user to another.
```cpp
  mesibo_int_t (*send_message)(mesibo_module_t *mod, mesibo_message_params_t *params,
                      const char *message, mesibo_uint_t len);
```
Parameters:
1. `mod` Pointer to mesibo module struct
2. `params` Pointer to message params struct. It contains message parameters such as 
`id`- Unique message identifier(For example it can be a pseudo-random number), `from`-  sender of the message, `to`-  message recipient, etc For more details refer [Data Structures](#data-structures).
3. `message` character buffer which contains the message data bytes
4. `len` containing the length of the message ie; number of bytes in the data buffer

Returns:
Integer : 0 on Success , -1 on failure

Example,
```cpp
    mesibo_message_params_t p;
    p.to = 'user_source';
    p.from = 'user_destination';
    p.id = rand();
    const char* message = "Hello from Module";
    mesibo_uint_t len = strlen(message);
    mod->send_message(mod, &p, message, len);
```

### http
This function can be used to make an HTTP request.
```cpp
  mesibo_int_t (*http)(mesibo_module_t *mod, const char *url, const char *post,
              mesibo_module_http_data_callback_t cb, void *cbdata,
              module_http_option_t *opt);
```
Parameters:
1. `mod` Pointer to mesibo module struct
2. `url` ServerURL, both http and https url are supported. You can also pass authentication information in url. For example, https://username:password@yourapiurl.com
3. `post` is a string that contains raw POST data.For example the part of the request after the question mark (e.g. "op=userdel" )
4. `cb` is the call back function pointer whose prototype should match `mesibo_module_http_data_callback_t`. You will get the response of your http request, asynchronously through this callback function. Refer the example [HTTP Callback Function](#http-callback-function) provided.
5. `cbdata` is a pointer to data of arbitrary user-defined type. This callback data is passed on to the callback function that you have passed in the previous argument.You can store data of any arbitrary type such as a C/C++ struct and pass it as callback data to your call back function. For more details refer to the [sample code]()
6. `opt` is the structure that contains `options` or additional parameters that you need to pass in your HTTP request such as extra_header,content_type, etc. For more details about the `module_http_option_t` structure, refer [Data Structures](#data-structures)

Returns:
Integer : 0 on Success , -1 on failure

Example,
If your REST URL  looks like https://api.mesibo.com/api.php?op=userdel&token=123434343xxxxxxxxx&uid=456, then you send the HTTP request as follows. 
```cpp
 const char* url = "https://app.mesibo.com/api.php"; //API endpoint
 const char* post = "op=userdel&token=123434343xxxxxxxxx&uid=456"; // POST Request Data
 mod->http(mod, url, post, mesibo_http_callback,NULL,NULL);
```

### HTTP Callback Function
The callback function reference,`cb` that you pass as a parameter should be defined as per the function prototype `mesibo_module_http_data_callback_t` in `module.h`.
```cpp
typedef int (*mesibo_module_http_data_callback_t)(void *cbdata,
                                                  mesibo_int_t state,
                                                  mesibo_int_t progress,
                                                  const char *buffer,
                                                  mesibo_int_t size);
```
The callback function takes the following parameters:  
1.`cbdata` Pointer to arbitrary data, which the response callback function may need. You pass this while calling the request function [http](#http).  
2.`state` An integer indicating the state of the response data being passed.   
```cpp
typedef enum {
  MODULE_HTTP_STATE_REQUEST,
  MODULE_HTTP_STATE_REQBODY,
  MODULE_HTTP_STATE_RESPHEADER,
  MODULE_HTTP_STATE_RESPBODY,
  MODULE_HTTP_STATE_DONE
} module_http_state_t;
```
3.`buffer` The response is delivered via the callback function asynchronously using buffers. This means, for example, a module can start sending the response from a backend server and stream it to the client before the module has received the entire response from the backend.
4.`size` Buffer size ie; Number of bytes in the buffer

If you do not want to stream the response immediately, you can keep copying the response bytes and save it to a buffer with each progress session and send the complete response to the client once the progress is complete. 
For example,
```cpp
static int mesibo_http_callback(void *cbdata, mesibo_int_t state,
                                mesibo_int_t progress, const char *buffer,
                                mesibo_int_t size) {
  tMessageContext *b = (tMessageContext *)cbdata;
  mesibo_module_t *mod = b->mod;
  mod->log(mod, 0, "===> progress %d state %d size %d\n", (int)progress,
           (int)state, (int)size);

  if (progress < 0) {
    mod->log(mod, 0, " Error in http callback \n");
    free(b);
    return -1;
  }

  if (state != MODULE_HTTP_STATE_RESPBODY) {
    mod->log(mod, 0, " Exit http callback \n");
    free(b);
    return 0;
  }

  if ((progress > 0) && (state == MODULE_HTTP_STATE_RESPBODY)) {
    memcpy(b->buffer + b->datalen, buffer, size);
    b->datalen += size;
  }

  if (progress == 100) {
    mod->log(mod, 0, "%.*s", b->datalen, b->buffer);
    free(b);
  }

  return 0;
}

```
### log
This function can be used to print to mesibo container logs.
```cpp
  mesibo_int_t (*log)(mesibo_module_t *mod, mesibo_uint_t level, const char *format,
             ...);

```
Parameters:
1. `mod` Pointer to mesibo module struct
2. `level` which defines the log level
3. `format` string for printing data which is similar to that of `printf` followed by the data to print.

Returns:
Integer : 0 on Success , -1 on failure

Example,
```cpp
mod->log(mod,0,"%s \n","Hello, from Mesibo Module!");
```
## Data Structures

### Message Parameters Structure
The C/C++ structure `mesibo_message_params_t` is used to define the various parameters of an incoming or outgoing message.
Message params is used as argument to functions such as `on_message`,`on_message_status`,`send_message`,etc.
```cpp
typedef struct mesibo_message_params_s {
  mesibo_uint_t aid;
  mesibo_uint_t id;  // original id, not the 64 bit
  mesibo_uint_t refid;
  mesibo_uint_t groupid;
  mesibo_uint_t flags;
  mesibo_uint_t type;
  mesibo_uint_t expiry;
  mesibo_uint_t to_online;

  uintptr_t reserved_0;
  uintptr_t reserved_1;

  char *to, *from;
} mesibo_message_params_t;
```
- `aid` - Application id.
- `id`  - id of the incoming message. For outgoing messages, id should be specified in `send_message` function.
- `refid` - Reference id (id of another message) to which the current message can be linked against.
- `groupid` - Group ID should be specified when sending message to a group, 0 for one-to-one messages. 
- `flags`- Message Flag - can be a combination of one or more flags
- `type` - Message Type, any arbitrary user-defined types
- `expiry` - Message Expiry for an outgoing message (time to live), in seconds
-`to_online` -  

### Mesibo User
```cpp
typedef struct  mesibo_user_s {
  mesibo_uint_t flags;
  char *address;
  mesibo_int_t online;

  uintptr_t reserved_0;
  uintptr_t reserved_1;
}mesibo_user_t;
```

### HTTP Options Structure
To pass`options` parameter of a HTTP request in the function [http()](#http) you use the C/C++ structure `module_http_option_t`
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

- `proxy` Proxy URL.You can pass authentication information here.  
- `content_type` Content-Type header. For example "application/json".
- `extra_header` Any custom headers you like to send, such as contain Authorisation header,etc
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
The configuration atrributes for a module can be provides as a configuaration list which shall be made available in the mesibo moduile initialization function, through the following structures
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
} module_configs_t;
```
`module_configs_t` contains `count`- the number of items in the configuration list &  a list of items of type  `module_config_item_t` - a structure containing a name-value pair.

## Memory Mangement
Data objects of various data types are configured and handled by Mesibo Modules. While some of them are user defined and user allocated, some data objects are configured internally and should be protected. Please go through this section carefully, so that you do not inadvertantly free memory that is protected/reserved by the module which may cause your module based application to fail.

Module Memory Map:
Freeable : Safe territory
Not freeable: Unsafe territory. Tread with caution

 
## Writing and Compiling Mesibo Modules
To write and build your Mesibo Module follow the steps below:

1. Download and copy all the required header files and C/C++ source file(s) into your working directory 
2. Include `module.h` in your code 
3. Initialize mesibo module as by providing a module name, your callback function references, etc. For a detailed example with code on writing a Mesibo Module, refer to the example [Building a chat-bot](#building-a-chat-bot)
4. To compile your module you can refer the sample `MakeFile` and modify the `TARGET` to the path where you need to place the resulting shared library accordingly. For example, `/etc/mesibo/module_<module name>.so`
```cmake
//Sample MakeFile to build Mesibo Module 
CC = gcc
FLAGS        = # -std=gnu99 -Iinclude
CFLAGS       = -fPIC -g #-pedantic -Wall -Wextra -ggdb3
LDFLAGS      = -shared
RM = rm -f

DEBUGFLAGS   = -O0 -D _DEBUG
RELEASEFLAGS = -O2 -D NDEBUG -combine -fwhole-program

TARGET  = /usr/lib64/mesibo/mesibo_mod_test.so
SOURCES = $(wildcard *.c)
HEADERS = $(wildcard *.h)
OBJECTS = $(SOURCES:.c=.o)

all: $(TARGET)

clean: 
        $(RM) *.o *.so $(TARGET)


$(TARGET): $(OBJECTS)
        @mkdir -p /usr/lib64/mesibo/
        $(CC) $(FLAGS) $(LDFLAGS) $(DEBUGFLAGS) -o $(TARGET) $(OBJECTS)

```

## Loading a Mesibo Module 
As described previously, a mesibo module is simply a shared library (.so file) that needs to be loaded at runtime which links with your main Mesibo instance.

So, how do you load your Mesibo module?
To do this ,you need to tell mesibo the details of the module you wish to load- where to find your module ie; the directory path where is your module is located and the name of the module -- the name of the C/C++ source file where you have defined your module.

For example, the path to your module could be `/usr/lib64/mesibo/mesibo_test.so`
and the name of your module could be `test` with `test.cpp` being your C/C++ source file.

You provide the directory path by mounting the directory path `<module path>` as a `-v` option when you run the Mesibo container. You also need to mount the directory `/etc/mesibo/` which contains your mesibo configuration file `mesibo.conf` where you need to specify the name of the module in the configuration file `/etc/mesibo/mesibo.conf` like so:
`module= <module name>`

```
sudo docker run  -v /certs:/certs -v  /usr/lib64/mesibo/:/usr/lib64/mesibo/ \
-v /etc/mesibo:/etc/mesibo  -p 5222:5222 \
-p 5228:5228 -p 80:80 -p 443:443 -p 4443:4443 -p 5443:5443 -p 513:513 -d \ 
mesibo/mesibo <app token> 
``` 


## Code references and Examples
 
### Building a chat-bot 
It is extremely simple to get started with Mesibo to build chatbots, which can integrate powerful analytical abilities in speech, image recognition, Natural Language processing, etc in your backend using loadable modules. You can interface with any tool or library of your choice such as Dialogflow, IBM Watson, Tensorflow, etc using REST endpoints.

![Module Chatbot Sample](module_chat_bot.jpg)

Let's look at how you can build a chatbot using Mesibo Modules:
- When you receive a message, you get the message text via the callback function `on_message` with the message data and it's associated message parameters as arguments. This message is the `query` to your chatbot.
- Now, you can create a chatbot module function that takes this message data/ text as input and sends it to a REST endpoint as HTTP Post data which contains the query using the function `http`. 
- In the function [http](),You pass the REST endpoint `url`, `post` data -which is as per the query format of your REST endpoint , callback function (where you will receive the response to your HTTP request) and Call back data (which is passed along to your callback function), `options` which will contain additional headers such as Authorisation header,etc (If required)
- Now based on your request, your chatbot model will process the query and send the appropriate response.
- In your callback function, you will receive the HTTP response for your query in the form of a JSON string. Send the  response using  `send_message` function.

You can refer to the [Sample Chatbot Module] source code which demonstrates building your module with a Dialogflow chatbot. 

The following is a step-by-step tutorial for building a chat-bot using Mesibo Module:

### 1. Create a C/C++ Source file
First let us choose a name for our module. Since we will be building a chatbot , let our module name be `chatbot`. We  will create a C/C++ Source file with the same name as that of the module. ie; `chatbot.cpp`. Copy the header file `module.h` into your working directory and include it in your code.
```cpp
#include "module.h"
```

### 2. Initialise the module
Now, we need to initialise our module by filling in the configuration details- module version, the name of our module and  the references of our module callback functions.

For a simple chatbot, we need to provide a callback function reference for `on_message`.  
`bot_on_message` : To get input query from a message. This function needs to be called whenever there is an incoming message and alert us. So, we pass this function reference to be the module's `on_message` callback function.

Following the naming convention of for the init function whcih is `mesibo_module_<module name>_init` the initialisation function for the module `chatbot` will be as defined below.

```cpp
//File : chatbot.cpp
int mesibo_module_chatbot_init(mesibo_module_t *m, mesibo_uint_t len) {
  if (sizeof(mesibo_module_t) != len) {
    m->log(m, 0, "module size mismatch\n");
    return -1;
  }

  if (MESIBO_MODULE_SIGNATURE != m->signature) {
    m->log(m, 0, "module signature mismatch\n");
    return -1;
  }

  m->version = 1;
  m->flags = 0;
  m->name = strdup("Sample Chatbot Module");
  m->on_message = bot_on_message;

  return 0;
}
```
### 3.`bot_on_message`

We need message containing queries from the end user only. `on_message` will alert you for any message sent or received. So, we filter these messages to match only those messages that are incoming from a particular user `bot_user`. 
For incoming messages from `bot_user` we process the message/query and send a response from the chat-bot.
For all other messages we pass the message as it is.
```cpp
static int bot_on_message(mesibo_module_t *mod, mesibo_message_params_t *p,
                          const char *message, mesibo_uint_t len) {
  mod->log(mod, 0, " %s on_message called\n", mod->name);
  mod->log(mod, 0, " aid %u from %s to %s id %u message %s\n", p->aid, p->from,
           p->to, (uint32_t)p->id, message);

  if (strcmp(p->from,"bot_user") == 0) {  // Filter messages based on sender of message
    mod->log(mod, 0, " Processing message from %s \n", p->from);
    //Don't modify original as other module will be use it 
    mesibo_message_params_t* np = (mesibo_message_params_t*)calloc(1,sizeof(mesibo_message_params_t));
    memcpy(np, p, sizeof(mesibo_message_params_t));
    bot_process_message(mod, np, message, len);
    return MESIBO_RESULT_CONSUMED;  // Process the message and CONSUME original
                                    // message
  } else
    return MESIBO_RESULT_PASS;  // PASS the message as it is
}
```
### 4. Processing the incoming message
To process the incoming query we need to send it to a chatbot model. Here, as an example we will be using [Dialogflow](https://dialogflow.com) as the chatbot provider and will make a an HTTP request to your Dialogflow chatbot. Your chatbot will use it's dialogflow model and send the appropriate respoonse to your HTTP request.

For more info on using Dialogflow and building chatbot models, checkout [Dialogflow Docs](https://dialogflow.com/docs).

To send an HTTP request to your chatbot endpoint,the POST data must be in a specific input format as defined by your chatbot API provider. 

> :warning: **Warning**: It is recommended to use the example for [Dialogflow v2 API](#dialogflow-v2-api) than the depracated v1 API. On October 23, 2019 the Dialogflow V1 API will be deprecated and shut down.

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
typedef struct _tMessageContext {
  mesibo_module_t *mod;
  mesibo_message_params_t *params;
  const char* from;
  const char* to;
  // To copy data in response
  char buffer[MIN_BUFFER_LEN];
  int datalen;
} tMessageContext;
```
On recieving the complete response from Dialogflow, we send the response to the original sender of the message and pass the message id of the query message as reference id for the response message. This way the client who sent the message will able to match the response recieved with the query sent. 

```cpp
static int bot_process_message(mesibo_module_t *mod, mesibo_message_params_t *p,
                               const char *message, mesibo_uint_t len) {
  mod->log(mod, 0, "Processing message from %s \n", p->from);

  // Dialogflow V2
  // Dialogflow API reference https://cloud.google.com/dialogflow/docs
  // POST https://dialogflow.googleapis.com/v2beta1/{session=projects/*/agent/sessions/*}:detectIntent
  // Change Session ID and project name accordingly
  char *base_url =
      "https://dialogflow.googleapis.com/v2beta1/projects/<project name>/"
      "agent/sessions/<session id>:detectIntent";
  const char *request_body =
      "{\"queryParams\":{\"timeZone\":\"Asia/"
      "Calcutta\"},\"queryInput\":{\"text\":{\"text\":\""; //Append Query Text
  int append_len = strlen("\",\"languageCode\":\"en\"}}}\0"); //Query language set to English
  char *raw_post_data =
      (char *)calloc(1, strlen(request_body) + len + append_len + 1);
  memcpy(raw_post_data, request_body, strlen(request_body));
  memcpy(raw_post_data + strlen(request_body), message, len);
  memcpy(raw_post_data + strlen(request_body) + len,
         "\",\"languageCode\":\"en\"}}}\0", append_len + 1);

  module_http_option_t *request_options =
      (module_http_option_t *)calloc(1, sizeof(module_http_option_t));

  // Your dialogflow service account key,Refer
  // https://cloud.google.com/docs/authentication/
  request_options->extra_header =
      "Authorization: Bearer "
      "yaxx.c."
      "Kl6bB3RdF81XC95YnV02j3LGhAYi0ZdlmrYKX5NveRB0SZx5oe0g1cRfc7xxxxxxxxxxxx-";
  request_options->content_type = "application/json";

  tMessageContext *message_context =
      (tMessageContext *)calloc(1, sizeof(tMessageContext));
  message_context->mod = mod;
  message_context->params = p;
  message_context->from = strdup(p->from);
  message_context->to = strdup(p->to);
  mod->log(
      mod, 0,
      " ===> Sending http for msg params :aid %u id %u from %s to %s  len %d\n",
      p->aid, p->id, p->from, p->to, len);

  mod->log(mod, 0, " %s %s %s %s \n", base_url, request_body, raw_post_data,
           request_options->extra_header);
  mod->http(mod, base_url, raw_post_data, bot_http_callback,
            (void *)message_context, request_options);

  return 0;
}
```  

### Dialogflow V1 API (depracated)
In the case of dialogflow v1 API (depracated),
the request url is  `https://api.dialogflow.com/v1/query?v=20150910`
and the POST data will contain the [query](https://dialogflow.com/docs/reference/agent/query#query_parameters_and_json_fields) string.
You also need to pass authentication information in the request header.

An HTTP query sample for dialogflow v1 API looks like below
```
  POST https://api.dialogflow.com/v1/query?v=20150910

  Headers:
  Authorization: Bearer YOUR_CLIENT_ACCESS_TOKEN
  Content-Type: application/json

  POST body:
  {
    "contexts": [
      "shop"
    ],
    "lang": "en",
    "query": "I need apples",
    "sessionId": "12345",
    "timezone": "America/New_York"
  }
    
```
Following this POST format we send an HTTP request using the function `http`. 

On recieving the response in the http callback function `bot_http_callback`(which we shall define in the next step), from Dialogflow we need to send the response back to the user who made the request. So we store the context of the received message ie; message parameters ,the sender of the message,the reciever of the message in the following structure and pass it as callback data. Note that you can store any data that you require to be passed to the http_callback function by modifying the tMessageContext structure accordingly.

```cpp
typedef struct _tMessageContext {
  mesibo_module_t *mod;
  mesibo_message_params_t *params;
  const char* from;
  const char* to;
  // To copy data in response
  char buffer[MIN_BUFFER_LEN];
  int datalen;
} tMessageContext;
```
On recieving the complete response from Dialogflow, we send the response to the original sender of the message and pass the message id of the query message as reference id for the response message. This way the client who sent the message will able to match the response recieved with the query sent. 

```cpp
static int bot_process_message(mesibo_module_t *mod, mesibo_message_params_t *p,
                               const char *message, mesibo_uint_t len) {
  mod->log(mod, 0, "Processing message from %s \n", p->from);
  char *base_url = "https://api.dialogflow.com/v1/query?v=20150910";
  char *request_body =
      "{'lang': 'en','sessionId': '12345','timezone': "
      "'America/New_York','query':";
  char *raw_post_data = (char *)calloc(1, strlen(request_body) + len + 2);
  memcpy(raw_post_data, request_body, strlen(request_body));
  memcpy(raw_post_data + strlen(request_body), message, len);
  memcpy(raw_post_data + strlen(request_body) + len, "}\0", 2);

  module_http_option_t *request_options =
      (module_http_option_t *)calloc(1, sizeof(module_http_option_t));
  // Your dialogflow CLIENT_ACCESS_TOKEN
  request_options->extra_header =
      "Authorization: Bearer 4b458b3feba0448fxxxxxxxxxxxxxxxx";
  request_options->content_type = "application/json";

  tMessageContext *message_context =
      (tMessageContext *)calloc(1, sizeof(tMessageContext));
  message_context->mod = mod;
  message_context->params = p;
  message_context->from = strdup(p->from);
  message_context->to = strdup(p->to);

  mod->http(mod, base_url, raw_post_data, bot_http_callback,
            (void *)message_context, request_options);

  return 0;
}

   }
```                           
### 5. Define the Callback function to receive the response from your bot
In the callback function, we will be getting the response in asynchronous blocks. So,we copy the response data into a temporary buffer and once the complete response is received(indicated by progress=100) we send the response back to user who sent the query.

```cpp

static int bot_http_callback(void *cbdata, mesibo_int_t state,
                             mesibo_int_t progress, const char *buffer,
                             mesibo_int_t size) {
  tMessageContext *b = (tMessageContext *)cbdata;
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
    // mod->log(mod, 0, " Exit http callback %.*s \n",buffer,size);
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
    p->from = strdup(params->to);
    p->to = strdup(params->from);  // User adress who sent the query is the recipient
    p->expiry = 3600;

    mod->log(mod, 0,
             " ===> Sending response :aid %u id %u from %s to %s  len %d\n",
             p->aid, p->id, p->from, p->to, b->datalen);
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
 module=chatbot
 ```
 Mount the directory containing your library which in this case is `/usr/lib64/mesibo/`, while running the mesibo container as follows. You also need to mount the directory containing the mesibo configuration file which in this case is `/etc/mesibo`
 ```
sudo docker run  -v /certs:/certs -v  /usr/lib64/mesibo/:/usr/lib64/mesibo/ \
-v /etc/mesibo:/etc/mesibo  -p 5222:5222 \
-p 5228:5228 -p 80:80 -p 443:443 -p 4443:4443 -p 5443:5443 -p 513:513 -d \ 
mesibo/mesibo <app token> 
 ```










