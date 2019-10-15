---
description: Mesibo Loadable Modules
keywords: open, source, on-premise, messaging, chat, voice, video
title: Mesibo Loadable Modules
---

Mesibo is designed by developers for developers. Its dynamically loadable module architecture lets you add new features and your own custom functionalities for deeper integration with your infrastructure, enabling you for unlimited creative possibilities. 

With Mesibo Modules you can build powerful chatbots, filters, remotely communicate with hardware for IoT and robotics, integrate with Machine learning and Scientific computing backend such as TensorFlow, DialogFlow, Matlab, etc and much more, all the way keeping your data secure and private in your premises or private cloud.  

This makes Mesibo, the most compelling real-time communication platform existing today. In this document, we will describe how you can build and use Mesibo Modules to unlock new possibilities and innovative solutions.

{% comment %} 
## Overview
0. Prerequisites
1. Overview of a Mesibo Module  
a. What is a Mesibo Module  
b. What can you build with a Mesibo Module  
c. How do Mesibo Modules work  

2. Anatomy of Mesibo Module  
a. Module Configuration Struct  
b. Module initialization  
c. Callback Functions  
d. Core Utility functions (Better name)  
e. Data Structures
3. Writing and Compiling Mesibo Modules 
4. Loading a Mesibo Module
5. Code references and Examples
{% endcomment %}

## Prerequisites 
- Running Mesibo On-Premise
- Knowledge of Building and deploying C/C++ shared libraries

## What is a Mesibo Module?

Mesibo Module is essentially a message processor which allows you to intercept each message and decide whether to pass the message as it is to the destination, drop it, or process it before sending it to the destination. For example,

- a **profinity filter module** can drop messages containing profanity 
- a **tranlator module** can translate each message before sending it to destination
- a **chatbot module** can analyze messages using various AI and machine learning tools like TensorFlow, DialogFlow, etc. and send an automatic reply. 

![Module Flowchart](https://github.com/Nagendra1997/mesibo-documentation/raw/master/Mesibo_Loadable_Modules (3).jpg)

The functionality of each module is programmed by you and its capability is limited only by your imagination.  Mesibo modules makes Mesibo a powerful communication platform.  



You can build Mesibo Module on top of the core platform, as a shared library(basically a .so file) and load it to extend the functionality of the Mesibo.
<img src="https://github.com/Nagendra1997/mesibo-documentation/blob/master/Mesibo_Loadable_Modules.jpg" width="1000" align='center'>

### How do Mesibo Modules work?
A Mesibo module is a shared library (`.so` file) which can be loaded at runtime by the Mesibo server. Mesibo then invokes various callback functions that you have defined in the share library whenever it receives messages or message status from your users. Your module can then decide what to do with those messages, for example:

 - Pass the message as it is
 - Drop it
 - Process further before sending it to the destination
 - Reply to the sender
   
<img src="https://github.com/Nagendra1997/mesibo-documentation/blob/master/Mesibo_Loadable_Modules (1).jpg" width="1000" align='center'>

You can load multiple modules, each having their own features and functionalities. You can specify all the modules to be loaded and the order in the Mesibo configuration file, `/etc/mesibo/mesibo.conf`. Mesibo will pass data to each module in the order modules were loaded. 

Create a mesibo module is extremely easy, for example, for example, you can implement a simple profanity filter module as follows.

### Building a profanity filter

- Implement a callback function to process all the incoming messages and pass it to mesibo - we will call it `on_message` function 
- When any user sends a message, Mesibo will invoke `on_message` callback function of your module with the message data and it's associated message parameters such as sender, expiry, flags, etc. 
- Your module can analyze the message to find any profanity or objectionable content and returns whether the message is safe and free of profanity or not. 
- if no profanity was found, you can `PASS` the message and safely send it to the recipient, else  you can `CONSUME` the unsafe message and prevent the message from reaching the receiver.

Now that you understand the overall functionality of a Mesibo module, let's dive deep into the technical details of what forms a mesibo module.

## Anatomy of a Mesibo Module

A Mesibo module has three parts:

- An initialization function which will be called by Mesibo once to initialize the module
- A set of callback functions that will be defined by the modules. These functions are called by the Mesibo as and when required
- A set of callable functions provided by the Mesibo that can be called by the module as required
- A configuration structure which declares aboved mentioned functions and other configuration information

### Mesibo Module Configuration 
A Mesibo Module is described by `mesibo_module_t` structure as defined below. This is one of the most important data structures used in the Mesibo module. 

```cpp
typedef struct mesibo_module_s {
        mesibo_uint_t   version;	/* module API Version */
        mesibo_uint_t   flags;		/* module Flags */
        const char      *name;		/* module name */
        
	/* callback functions defined by the module */

        int             (*cleanup)(mesibo_module_t *mod);
        int             (*on_message)(mesibo_module_t *mod, mesibo_message_params_t *params, 
				const char *message, mesibo_uint_t len);
        int             (*on_message_status)(mesibo_module_t *mod, mesibo_message_params_t *params, mesibo_uint_t  status);
        int             (*on_call)(mesibo_module_t *mod);
        int             (*on_call_status)(mesibo_module_t *mod);
        
	mesibo_uint_t   signature;	/* module signature */

	/* functions defined by mesibo and can be called by module */

        int             (*send_message)(mesibo_module_t *mod, mesibo_message_params_t *params, const char *message, 
                         mesibo_uint_t len);
        int             (*http)(mesibo_module_t *mod, const char *url, const char *post, 
                         mesibo_module_http_data_callback_t cb, void *cbdata, module_http_option_t *opt);
        int             (*log)(mesibo_module_t *mod, mesibo_uint_t level, const char *format, ...);

} mesibo_module_t;

```

In the next section, we will learn how to initialize module configuration structure. Before that, it is essential to know how Mesibo knows about your modules and loads into the memory. 

### Loading a module
As described previously, a mesibo module is a shared library (`.so` file) that is loaded at runtime by the Mesibo. In order for Mesibo to load your module, you need to tell mesibo about the module you wish to load for example, path where your module is located and the name of the module shared library.

For example, the path to your module could be `/usr/lib64/mesibo/mesibo_test.so`
and the name of your module could be `test` with `test.c` being your C source file.


### Module initialization
The above module structure initialization is performed by a callback function, the prototype for which can be found in the file [module.h](). This function is automatically called by mesibo when the module is constructed. The naming convention for this function is `mesibo_module_<module name>_init`

The function takes two parameters:
1. `mod` of type `mesibo_module_t*` - Pointer to mesibo module struct
2. `len` of type `mesibo_uint_t`

It is important to note that the size of the configuration structure `mesibo_module_t` defined should be equal to `len` and `signature` of your module should match with the defined `MESIBO_MODULE_SIGNATURE`.  You must check the module structure length and singature to ensure that structure is aligned as expected. 


For example,for a module named `test` (** which is defined in a file named `test.c`) the initialisation function looks like below.
```cpp
int mesibo_module_test_init(mesibo_module_t *m, mesibo_uint_t len) {
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
  m->name = strdup("Sample Module");
  m->cleanup = test_cleanup;
  m->on_message = test_on_message;
  m->on_message_status = test_on_message_status;

  return 0;
}
```
test_cleanup, test_on_message, test_on_message_status are functions that have been defined by you and serve as the corresponding callback functions cleanup,on_message & on_message_status .

### b. Callback functions
There are a set of callback functions that need to be initialized by the caller of the module. These callback functions are called by Mesibo based on different events. Callback functions that you provide should have the same signature as defined in the `mesibo_module_t` struct.
For a detailed explanation of callback functions and their prototypes refer [Callback functions]

`on_message`: When a message is sent from one endpoint to another in your application 

`on_messagestatus`: When a message is sent and you will receive a status update

`on_call`: When a call is received

`on_call_status`: When a call status is received

`cleanup` When module completes its work and is unloaded


### c. Core utility functions
Some functions are initialized by Mesibo which you can use. For a detailed explanation refer [Core Utility functions]()

`send_message` To send message from one user to another

`http` To send HTTP ie; REST type request

`log` To print logs to mesibo server logs


### Callback Functions
There are different callback functions that you can define in your module and instruct mesibo to call these functions under different events such as : 
- When you get a message
- When you send a message and get status of the sent message (Sent, Received or delivered)
- When you receive a call
- When you complete the module process and reclaim memory/ perform the cleanup

You need to provide the callback functions with the same prototype while initializing the module: ie; same number of arguments, data types of arguments and return type.All function prototypes are found in the file [module.h]().

There are two important observations for all call back functions:
- Mesibo Module Structure pointer: All callback functions include `the mesibo_module_t` structure pointer as the first argument
- Pass or Consume mechanism according to return value: Each function returns an integer value which is defined in the file [module.h]()     
`MESIBO_RESULT_PASS` pass the data as it is and the recipient is notified  
`MESIBO_RESULT_CONSUMED` where the data is consumed and the recipient is not notified of this data



Let's look in detail at the different callback functions and their prototypes:

### on_message 
This function is called when the module receives a message.
```C
int (*on_message)(mesibo_module_t *mod, mesibo_message_params_t *params, const char *message, mesibo_uint_t len)
```
Parameters:
1. `mod` Pointer to mesibo module struct
2. `params` Pointer to message params struct. It contains message parameters such as 
`id`- Unique message identifier(For example it can be a pseudo-random number), `from`-  sender of the message, `to`-  message recipient, etc For more details refer [Data Structures]().
3. `message` character buffer  which contains the message data bytes
4. `len` contains the length of the message ie; number of bytes in the data buffer

Returns:

`MESIBO_RESULT_CONSUMED` if message data is to be consumed and the recipient will not receive THIS message data.

`MESIBO_RESULT_PASS` pass the message data and parameters as it is to the recipient

### on_message_status
This function is called when a message is sent from the module and you receive the status of the message.
```C
int (*on_message_status)(mesibo_module_t *mod, mesibo_message_params_t *params, mesibo_uint_t  status)
```
Parameters:
1. `mod` Pointer to mesibo module struct
2. `params` Pointer to message params struct. It contains message parameters such as 
`id`- Unique message identifier(For example it can be a pseudo-random number), `from`-  sender of the message, `to`-  message recipient, etc For more details refer [Data Structures]().
3. `status` containing the status of the sent message which corresponds to different [status codes](https://mesibo.com/documentation/api/real-time-api/data-structures/#messageparams) such as `MSGSTATUS_SENT`,`MSGSTATUS_DELIVERED`,`MSGSTATUS_READ`, etc

Returns:

`MESIBO_RESULT_CONSUMED` if message data is to be consumed and the recipient will not be notified of THIS status data.

`MESIBO_RESULT_PASS` pass the status data and parameters as it is to the recipient

### on_cleanup
This function is called when the module process is complete and to clean up.
```C
int (*cleanup)(mesibo_module_t *mod)
```
### on_call
```C
int (*on_call)(mesibo_module_t *mod)
```
### on_call_status
```C
int (*on_call_status)(mesibo_module_t *mod)
```
##  Core Utility functions
These functions are initialized by Mesibo and you can utilize these functions to send a message, send an HTTP request, print logs, etc. Please note, that unlike the callback functions you need not pass any function references to initialize these functions as they are internally defined and initialized by Mesibo.

Let's look at the syntax of these functions.

### send_message
This function can be used to send a message from one end-point or user to another.
```C
int (*send_message)(mesibo_module_t *mod, mesibo_message_params_t *params, const char *message, mesibo_uint_t len);
```
Parameters:
1. `mod` Pointer to mesibo module struct
2. `params` Pointer to message params struct. It contains message parameters such as 
`id`- Unique message identifier(For example it can be a pseudo-random number), `from`-  sender of the message, `to`-  message recipient, etc For more details refer [Data Structures]().
3. `message` character buffer which contains the message data bytes
4. `len` containing the length of the message ie; number of bytes in the data buffer

Returns:
Integer : 0 on Success , -1 on failure

Example,
```C
    mesibo_message_params_t    p;
    p.to = 'user_source';
    p.from = 'user_destination';
    p.id = rand();
    const char* message = "Hello from Module";
    mesibo_uint_t len = strlen(message);
    mod->send_message(mod, &p, message, len);
```

### http
This function can be used to make an HTTP request.
```C
int (*http)(mesibo_module_t *mod, const char *url, const char *post, mesibo_module_http_data_callback_t cb, void *cbdata, module_http_option_t *opt)
```
Parameters:
1. `mod` Pointer to mesibo module struct
2. `url` ServerURL, both http and https url are supported. You can also pass authentication information in url. For example, https://username:password@yourapiurl.com
3. `post` is a string that contains raw POST data.For example the part of the request after the question mark (e.g. "op=userdel" )
4. `cb` is the call back function pointer whose prototype should match `mesibo_module_http_data_callback_t`. You will get the response of your http request, asynchronously through this callback function. Refer the example [HTTP Callback Function]() provided.
5. `cbdata` is a pointer to data of arbitrary user-defined type. This callback data is passed on to the callback function that you have passed in the previous argument.You can store data of any arbitrary type such as a C struct and pass it as callback data to your call back function. For more details refer to the [sample code]()
6. `opt` is the structure that contains `options` or additional parameters that you need to pass in your HTTP request such as extra_header,content_type, etc. For more details about the `module_http_option_t` structure, refer [Data Structures]()

Returns:
Integer : 0 on Success , -1 on failure

Example,
If your REST URL  looks like https://api.mesibo.com/api.php?op=userdel&token=123434343xxxxxxxxx&uid=456, then you send the HTTP request as follows. 
```C
 const char* url = "https://app.mesibo.com/api.php"; //API endpoint
 const char* post = "op=userdel&token=123434343xxxxxxxxx&uid=456"; // POST Request Data
 mod->http(mod, url, post, mesibo_http_callback,NULL,NULL);
```

### HTTP Callback Function
The callback function reference,`cb` that you pass as a parameter should be defined as per the function prototype `mesibo_module_http_data_callback_t` in `module.h`.
```C
typedef int (*mesibo_module_http_data_callback_t)(void *cbdata, mesibo_int_t state, mesibo_int_t progress, const char *buffer, mesibo_int_t size);
```
The callback function takes the following parameters:  
1.`cbdata` Pointer to arbitrary data, which the response callback function may need. You pass this while calling the request function `http`.  
2.`state` An integer indicating the state of the response data being passed.   
```C
typedef enum {MODULE_HTTP_STATE_REQUEST, MODULE_HTTP_STATE_REQBODY, MODULE_HTTP_STATE_RESPHEADER, MODULE_HTTP_STATE_RESPBODY, MODULE_HTTP_STATE_DONE} module_http_state_t;
```
3.`buffer` The response is delivered via the callback function asynchronously using buffers. This means, for example, a module can start sending the response from a backend server and stream it to the client before the module has received the entire response from the backend.
4.`size` Buffer size ie; Number of bytes in the buffer

If you do not want to stream the response immediately, you can keep copying the response bytes and save it to a buffer with each progress session and send the complete response to the client once the progress is complete. 
For example,
```C
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
```C
int (*log)(mesibo_module_t *mod, mesibo_uint_t level, const char *format, ...)
```
Parameters:
1. `mod` Pointer to mesibo module struct
2. `level` which defines the log level
3. `format` string for printing data which is similar to that of `printf` followed by the data to print.

Returns:
Integer : 0 on Success , -1 on failure

Example,
```C
mod->log(mod,0,"%s \n","Hello, from Mesibo Module!");
```
## Data Structures

### Message Parameters Structure
The C structure `mesibo_message_params_t` is used to define the various parameters of an incoming or outgoing message.
Message params is used as argument to functions such as `on_message`,`on_message_status`,`send_message`,etc.
```C
typedef struct mesibo_message_params_s {
    mesibo_uint_t     aid;
    mesibo_uint_t     id; 
    mesibo_uint_t     refid;
    mesibo_uint_t     groupid;
    mesibo_uint_t     flags;
    mesibo_uint_t     type;
    mesibo_uint_t     expiry;
    mesibo_uint_t     to_online;

    char     *to, *from;
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

### HTTP Options Structure
To pass`options` parameter of a HTTP request in the function `http()` (which is described in the previous section, Core  Utility Functions) you use the C structure `module_http_option_t`
```C
typedef struct _module_http_option_t {
    const char *proxy;

    //body or post data
    const char *content_type; //body content type

    const char *extra_header;
    const char *user_agent;
    const char *referrer;
    const char *origin;
    const char *cookie;
    const char *encoding; // could be gzip, deflate, identity, br (do not use 'compress' which is obsolete)
    const char *cache_control; //cache control and expiry
    const char *accept;
    const char *etag;
    mesibo_uint_t ims; //if modified since, gmt time

    mesibo_uint_t maxredirects;

    mesibo_uint_t conn_timeout, header_timeout, body_timeout, total_timeout;

    mesibo_uint_t retries;

} module_http_option_t;
```
### Basic Options Fields  

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

 
## 3. Writing and Compiling Mesibo Modules
To write and build your Mesibo Module follow the steps below:

1. Download and copy all the required header files and C source file(s) into your working directory 
2. Include `module.h` in your code 
3. Initialize mesibo module as by providing a module name, your callback function references, etc. For a detailed example with code on writing a Mesibo Module, refer to the example [Building a chat-bot]()
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

## 4. Loading a Mesibo Module 
As described previously, a mesibo module is simply a shared library (.so file) that needs to be loaded at runtime which links with your main Mesibo instance.
initialized your module, which is used to build your module.

So, how do you load your Mesibo module?
To do this ,you need to tell mesibo the details of the module you wish to load- where to find your module ie; the directory path where is your module is located and the name of the module -- the name of the C source file where you have defined your module.

For example, the path to your module could be `/usr/lib64/mesibo/mesibo_test.so`
and the name of your module could be `test` with `test.c` being your C source file.

You provide the directory path by mounting the directory path `<module path>` as a `-v` option when you run the Mesibo container. You also need to mount the directory `/etc/mesibo/` which contains your mesibo configuration file `mesibo.conf` where you need to specify the name of the module in the configuration file `/etc/mesibo/mesibo.conf` like so:
`module= <module name>`

```
sudo docker run  -v /certs:/certs -v  /usr/lib64/mesibo/:/usr/lib64/mesibo/ -v /etc/mesibo:/etc/mesibo  -p 5222:5222 \
-p 5228:5228 -p 80:80 -p 443:443 -p 4443:4443 -p 5443:5443 -p 513:513 -it mesibo/mesibo \
          <app token> 
``` 


## 5. Code references and Examples
 
### Building a chat-bot 
It is extremely simple to get started with Mesibo to build chatbots, which can integrate powerful analytical abilities in speech, image recognition, Natural Language processing, etc in your backend using loadable modules. You can interface with any tool or library of your choice such as DialogFlow, IBM Watson, TensorFlow, etc using REST endpoints.

<img src="https://github.com/Nagendra1997/mesibo-documentation/blob/master/Mesibo_Loadable_Modules (4).jpg" width="1000" align='center'>

Let's look at how you can build a chatbot using Mesibo Modules:
- When you receive a message, you get the message text via the callback function `on_message` with the message data and it's associated message parameters as arguments. This message is the `query` to your chatbot.
- Now, you can create a chatbot module function that takes this message data/ text as input and sends it to a REST endpoint as HTTP Post data which contains the query using the function `http`. 
- In the function [http](),You pass the REST endpoint `url`, `post` data -which is as per the query format of your REST endpoint , callback function (where you will receive the response to your HTTP request) and Call back data (which is passed along to your callback function), `options` which will contain additional headers such as Authorisation header,etc (If required)
- Now based on your request, your chatbot model will process the query and send the appropriate response.
- In your callback function, you will receive the HTTP response for your query in the form of a JSON string. Send the  response using  `send_message` function.

You can refer to the [Sample Chatbot Module] source code which demonstrates building your module with a DialogFlow chatbot. 

The following is a step-by-step tutorial for building a chat-bot using Mesibo Module:

### 1. Create a C Source file
First let us choose a name for our module. Since we will be building a chatbot , let our module name be `chatbot`. We  will create a C Source file with the same name as that of the module. ie; `chatbot.c`. Copy the header file `module.h` into your working directory and include it in your code.
```C
#include "module.h"
```

### 2. Initialise the module
Now, we need to initialise our module by filling in the configuration details- module version, the name of our module and  the references of our module callback functions.

For a simple chatbot, we need to provide a callback function reference for `on_message`.  
`bot_on_message` : To get input query from a message. This function needs to be called whenever there is an incoming message and alert us. So, we pass this function reference to be the module's `on_message` callback function.

Following the naming convention of for the init function whcih is `mesibo_module_<module name>_init` the initialisation function for the module `chatbot` will be as defined below.

```C
//File : chatbot.c
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
```C
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
To process the incoming query we need to send it to a chatbot model. Here, as an example we will be using [DialogFlow](https://dialogflow.com) as the chatbot provider and will make a an HTTP request to your DialogFlow chatbot. Your chatbot will use it's dialogFlow model and send the appropriate respoonse to your HTTP request.

For more info on using DialogFlow and building chatbot models, checkout [DialogFlow Docs](https://dialogflow.com/docs).

To send an HTTP request to your chatbot endpoint,the POST data must be in a specific input format as defined by your chatbot API provider. 

In the case of dialogFlow,
the request url is  `https://api.dialogflow.com/v1/query?v=20150910`
and the POST data will contain the [query](https://dialogflow.com/docs/reference/agent/query#query_parameters_and_json_fields) string.
You also need to pass authentication information in the request header.

An HTTP query sample for dialogFlow looks like below
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

On recieving the response in the http callback function `bot_http_callback`(which we shall define in the next step), from DialogFlow we need to send the response back to the user who made the request. So we store the context of the received message ie; message parameters ,the sender of the message,the reciever of the message in the following structure and pass it as callback data. Note that you can store any data that you require to be passed to the http_callback function by modifying the tMessageContext structure accordingly.

```C
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
On recieving the complete response from DialogFlow, we send the response to the original sender of the message and pass the message id of the query message as reference id for the response message. This way the client who sent the message will able to match the response recieved with the query sent. 

```C
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
  // Your dialogFlow CLIENT_ACCESS_TOKEN
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

```C

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
 sudo docker run  -v /certs:/certs -v  /usr/lib64/mesibo/:/usr/lib64/mesibo/ -v /etc/mesibo:/etc/mesibo  -p 5222:5222 \
-p 5228:5228 -p 80:80 -p 443:443 -p 4443:4443 -p 5443:5443 -p 513:513 -it mesibo/mesibo \
          <app token> 
 ```










