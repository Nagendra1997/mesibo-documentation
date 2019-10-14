# Mesibo Loadable Modules

Mesibo is designed by developers for developers. Its dynamically loadable module architecture lets you load your code for deeper integration with your infrastructure, enabling you for unlimited creative possibilities. 

With Mesibo Modules you can build powerful chatbots, filters, remotely communicate with hardware for IoT and robotics, integrate with Machine learning and Scientific computing backend such as TensorFlow, DialogFlow, Matlab, etc and much more, all the way keeping your data secure and private in your servers.  

This makes Mesibo, the most compelling real-time communication platform existing today.
Let's look at how you can build and use Mesibo Modules to unlock new possibilities and innovative solutions.

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

## Prerequisites 
- Running Mesibo On-Premise
- Getting Started Guide
- Knowledge of Building and deploying C/C++ shared libraries
- Request and Callback mechanism


## What is a Mesibo Module?

Mesibo Module is essentially a message processor which allows you to :
- Capture each message and decide whether to pass the message as it is or consume it
- Alter,block or filter each message
- Process the message and send an automatic response

<img src="https://github.com/Nagendra1997/mesibo-documentation/blob/master/Mesibo_Loadable_Modules (2).jpg" width="500" align='center'>

Mesibo offers a powerful communication platform which can be simplified into two major functionalities:
- Sending Data
- Receiving data

You can build Mesibo Module on top of the core platform, as a shared library(basically a .so file) and load it to extend the functionality of the Mesibo.
<img src="https://github.com/Nagendra1997/mesibo-documentation/blob/master/Mesibo_Loadable_Modules.jpg" width="1000" align='center'>

### What can you build with a Mesibo Module?
Mesibo modules can be built and loaded at runtime. The functionality of the modules is programmed by you and its capability is limited only by your imagination. 

For example, let us see how you can implement a simple profanity filter using a Mesibo Module.
### Building a profanity filter
You can easily build a filter module on top of Mesibo. 
- When you receive a message, you get the message text via the callback function `on_message` with the message data and it's associated message parameters as arguments. 
- Now, you can create a module that takes this message data/ text as input, analyze the text to find any profanity or objectionable content and return whether the message is safe and free of profanity or not. 
- Based on the profanity filter module's output, you can PASS the message and safely send it to the recipient using the `send_message` function or you can CONSUME the unsafe message and prevent the message from reaching the receiver.

### How do Mesibo Modules work?
A mesibo module performs two functions by linking  with your main Mesibo instance
- Recieve request/data through callback functions
- Process the data and send the result

<img src="https://github.com/Nagendra1997/mesibo-documentation/blob/master/Mesibo_Loadable_Modules (2).jpg" width="1000" align='center'>

The mesibo API instance invokes and communicates with any number of mesibo modules to perform various operation instructed by you. Mesibo can pass data to a chain of mesibo modules where the output of one module is piped to the input of another and the final result obtained from the processing of the module chain is sent back to the client.

Now that you understand the overall functionality of a Mesibo module, let's dive deep into the technical details of what forms a mesibo module.

## Anatomy of Mesibo Module

## Mesibo Module Configuration Struct
A Mesibo Module is described by a structure as defined below. This is one of the most important data structures used in the Mesibo module. 
```C
typedef struct mesibo_module_s {
        mesibo_uint_t   version;
        mesibo_uint_t   flags;
        const char      *name;
        
        int             (*cleanup)(mesibo_module_t *mod);
        int             (*on_message)(mesibo_module_t *mod, mesibo_message_params_t *params, const char *message,         
                         mesibo_uint_t len);
        int             (*on_message_status)(mesibo_module_t *mod, mesibo_message_params_t *params, mesibo_uint_t  status);
        int             (*on_call)(mesibo_module_t *mod);
        int             (*on_call_status)(mesibo_module_t *mod);
        mesibo_uint_t   signature;

        //These functions will be initialized by Mesibo
        int             (*send_message)(mesibo_module_t *mod, mesibo_message_params_t *params, const char *message, 
                         mesibo_uint_t len);
        int             (*http)(mesibo_module_t *mod, const char *url, const char *post, 
                         mesibo_module_http_data_callback_t cb, void *cbdata, module_http_option_t *opt);
        int             (*log)(mesibo_module_t *mod, mesibo_uint_t level, const char *format, ...);

} mesibo_module_t;

```
The mesibo module configuration contains:

### a. Basic Configuration details
`version`: Module Version 

`flags`: 

`name`: Name of your module which is a string 

`signature`: Module Signature which needs to be verified in the initialization step

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


### Module initialization
The above module structure initialization is performed by a callback function, the prototype for which can be found in the file [module.h](). This function is automatically called by mesibo when the module is constructed. The naming convention for this function is `mesibo_module_<module name>_init`

The function takes two parameters:
1. `mod` of type `mesibo_module_t*` - Pointer to mesibo module struct
2. `len` of type `mesibo_uint_t`

It is important to note that the size of the configuration structure `mesibo_module_t` defined should be equal to `len` and `signature` of your module should match with the defined `MESIBO_MODULE_SIGNATURE`.  


For example,for a module named `test` (** which is defined in a file named `test.c`) the initialisation function looks like below.
```C
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
2. `url` is a string which contains the path of the request, e.g. "api.mesibo.com/api.php"
3. `post` is a string that contains the POST data.For example the part of the request after the question mark (e.g. "op=userdel" )
4. `cb` is the call back function pointer whose prototype should match `mesibo_module_http_data_callback_t`. You will get the response of your http request, asynchronously through this callback function. Refer the example [HTTP Callback Function]() provided.
5. `cbdata` is a pointer to data of arbitrary user-defined type. This callback data is passed on to the callback function that you have passed in the previous argument.You can store data of any arbitrary type such as a C struct and pass it as callback data to your call back function. For more details refer to the [sample code]()
6. `opt` is the structure that contains `options` or additional parameters that you need to pass in your HTTP request such as extra_header,content_type, etc. For more details about the `module_http_option_t` structure, refer [Data Structures]()

Returns:
Integer : 0 on Success , -1 on failure

Example,
If your REST URL  looks like https://api.mesibo.com/api.php?op=userdel&token=123434343xxxxxxxxx&uid=456, then you send the HTTP request as follows. 
```C
 tMessageContext *b = (tMessageContext *)calloc(1, sizeof(tMessageContext));
 b->mod = mod;
 const char* url = "https://app.mesibo.com/api.php"; //API endpoint
 const char* post = "op=userdel&token=123434343xxxxxxxxx&uid=456" // POST Request Data
 mod->http(mod, url, post, mesibo_http_callback,(void*)b,NULL);
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
<Will refer http docs>
`content_type` includes application/json,etc
`extra_header` can contain Authorisation header ,etc
 
## 3. Writing and Compiling Mesibo Modules
To write and build your Mesibo Module follow the steps below:

1. Download the example module code, and copy all the required header files and C source file(s) into your working directory 
2. Open a code editor
3. Include `module.h` in your code 
4. Initialize mesibo module as follows by providing a module name, your callback function references, etc
```
```
5. To compile your module you can refer the sample `MakeFile` provided which builds and places the resulting shared library in `/etc/mesibo/module_<module name>.so`

## 4. Loading a Mesibo Module 
As described previously, a mesibo module is simply a shared library (.so file) that needs to be loaded at runtime which links with your main Mesibo instance.

So, how do you load your Mesibo module?
To do this ,you need to tell mesibo the details of the module you wish to load- where to find your module ie; the directory path where is your module is located and the name of the module -- the name of the C source file where you have defined and initialized your module, which is used to build your module.

For example, the path to your module could be `/usr/lib64/mesibo/mesibo_test.so`
and the name of your module could be `test` with `test.c` being your C source file.

You provide the directory path by mounting the directory path `<module path>` as a `-v` option when you run the Mesibo container. You also need to mount the directory `/etc/mesibo/` which contains your mesibo configuration file `mesibo.conf`.
For example,
```
sudo docker run  -v /certs:/certs -v  /usr/lib64/mesibo/:/usr/lib64/mesibo/ -v /etc/mesibo:/etc/mesibo  -p 5222:5222 \
-p 5228:5228 -p 80:80 -p 443:443 -p 4443:4443 -p 5443:5443 -p 513:513 -it mesibo/mesibo \
          <app token> 
``` 
You need to specify the name of the module in the configuration file `/etc/mesibo/mesibo.conf` like so:
`module= <module name>`

## 5. Code references and Examples
 
### Building a chat-bot 
It is extremely simple to get started with Mesibo to build chatbots, which can integrate powerful analytical abilities in speech, image recognition, Natural Language processing, etc in your backend using loadable modules. You can interface with any tool or library of your choice such as DialogFlow, IBM Watson, TensorFlow, etc using REST endpoints.

Let's look at how you can build a chatbot using Mesibo Modules:
- When you receive a message, you get the message text via the callback function `on_message` with the message data and it's associated message parameters as arguments. This message is the `query` to your chatbot.
- Now, you can create a chatbot module function that takes this message data/ text as input and sends it to a REST endpoint as HTTP Post data which contains the query using the function `http`. 
- In the function [http](),You pass the REST endpoint `url`, `post` data -which is as per the query format of your REST endpoint , callback function (where you will receive the response to your HTTP request) and Call back data (which is passed along to your callback function), `options` which will contain additional headers such as Authorisation header,etc (If required)
- Now based on your request, your chatbot model will process the query and send the appropriate response.
- In your callback function, you will receive the HTTP response for your query in the form of a JSON string. Extract the response text(As per your format) and send the text back as a response using the `send_message` function.

You can refer to the sample chatbot module which demonstrates building your module with a dialog flow chatbot. 











