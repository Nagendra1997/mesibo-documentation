# Dynamically Loadable Modules and Scripting

Mesibo is designed by developers for developers. Its dynamically loadable module architecture lets you load your own code for deeper integration with your infrastructure, enabling you for unlimited creative possibilities. 

This makes Mesibo, the most compelling real-time communication platform existing today.
Let's look at how you can build and use Mesibo Modules to unlock new possibilities and innovative solutions.

## Overview
0. Prerequisites
1. Overview of a Mesibo Module  
   a. What is a Mesibo Module  	
   b. What can you build with a Mesibo Module

   c. How do Mesibo Modules work

   d. Loading a Mesibo Module 

3. Anatomy of Mesibo Module
   a. Module Configuration Struct
   
   b. Module initialisation
   
   c. Callback Functions
   
   d. Core API functions (Better name)
   
   e. Data types and structures
4. Writing and Compiling Mesibo Modules 
5. Loading modules
5. Code references and Examples

## Prerequisites 
- Running Mesibo On Premise
- Getting Started Guide
- Knowledge of Building and deploying C/C++ shared libraries
- Request and Callback mechanism
<Explain>

## What is a Mesibo Module?

Mesibo Module is a shared library(basically a .so file) that extends the functionality of Mesibo. Mesibo offers a powerful communication platform which can be simplified into two major functionalities:
- Sending Data
- Recieving data

You can build on top of this core platform, by extending the functionality of Mesibo using a Mesibo Module.

### What can you build with a Mesibo Module?
Mesibo modules can be built and loaded at runtime. The functionality of the modules is programmed by you and its capability is limited only by your imagination. 

For example, let us see how you can implement a simple profanity filter using a Mesibo Module.
### Building a profanity filter
You can easily build a filter module on top of Mesibo. 
- When you recieve a message ,you get the message text via the callback function `on_message` with the message data and it's associated message parameters as arguments. 
- Now, you can create a module that takes this message data/ text as input,analyse the text to find any profanity or objectionable content and return whether the message is safe and free of profanity or not. 
- Based on the profanity filter module's output, you can PASS the message and safely send it to the recipient using the `send_message` function or you can CONSUME the unsafe message and prevent the message from reaching the reciever.

### How do Mesibo Modules work?
A mesibo module performs two functions by linking  with your main Mesibo instance
- Recieve request/data through callback functions
- Process the data and send the result

The mesibo API instance invokes and communicates with any number of mesibo modules to perform various operation instructed by you. Mesibo can pass data to a chain of mesibo modules where the output of one module is piped to the input of anotherand the final result obtained from the processing of the module chain is sent back to the client.

### Loading a Mesibo Module 
As described previously, a mesibo module is simply as shared library (.so file) that needs to be loaded at runtime which links with your main Mesibo instance.

So,how do you load your Mesibo module?
To do this ,you need to tell mesibo the details of the module you wish to load- where to find your module ie; the directory path where is your module is located and the name of the module -- the name of the C source file where you have defined and initialised your module,which is used to build your module.

For example, the path to your module could be `/usr/lib64/mesibo/mesibo_test.so`
and the name of your module could be `test` with `test.c` being your C source file.

You provide the directory path by mounting the directory path `<module path>` as a `-v` option when you run the Mesibo container.
For example,
```
sudo docker run  -v /certs:/certs -v  /usr/lib64/mesibo/:/usr/lib64/mesibo/ -v /etc/mesibo:/etc/mesibo  -p 5222:5222 \
-p 5228:5228 -p 80:80 -p 443:443 -p 4443:4443 -p 5443:5443 -p 513:513 -it mesibo/mesibo \
	      <app token> 
``` 
You need to specify the name of the module in the configuration file `/etc/mesibo/mesibo.conf` like so:
`module= <module name>`

Now that you understand the overall functionality of a Mesibo module, let's dive deep into the techincal details of what forms a mesibo module.

## Anatomy of Mesibo Module

## Mesibo Module Configuration Struct
A Mesibo Module is described by a structure as defined below. This is one of the most important data structures used in Mesibo module. 
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

`signature`: Module Signature which needs to be verified in the initialisation step

### b. Callback functions
There are a set of callback functions which needs to be initialised by the caller of the module. These callback functions are called by Mesibo based on different events. Callback functions that you provide should have the exact same signature as defined in the `mesibo_module_t` struct.
For a detailed explanation of callback functions and their prototypes refer [Callback functions]

`on_message`: When a message is sent from on endpoint to another in your application 

`on_messagestatus` : When a message is sent and you will recieve a status update

`on_call`: When a call is recieved

`on_call_status`: When a call status is recieved

`cleanup` When module completes its work and is unloaded


### c. Core utility functions
There are functions which are initialised by Mesibo which you can use. For a detailed explanation refer [Core API functions]()

`send_message` To send message from one user to another

`http` To send http ie; REST type request

`log` To print logs to mesibo server logs


### Module initialisation
The above module structure initialisation is performed by a callback function,the prototype for which can  be found in the file [module.h]().This function is automatically called by mesibo when module is constructed. The naming convention for this function is `mesibo_module_<module name>_init`

The function takes two parameters:
1. `mod` of type `mesibo_module_t*` - Pointer to mesibo module struct
2. `len` of type `mesibo_uint_t`

It is important to note that the size of configuration structure `mesibo_module_t` defined should be equal to `len`and `signature` of your module should match with the defined `MESIBO_MODULE_SIGNATURE`.  


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
- When you send a message and get status of the sent message (Sent,Recieved or delievered)
- When you recieve a call
- When you complete the module process and reclaim memory/ perform cleanup

You need to provide the callback functions with same prototype while initialising the module: ie; same number of arguments,data types of arguments and return type.All function prototypes are found in the file [module.h]().

There are two important observations for all call back functions:
- Mesibo Module Structure pointer: All callback functions include `the mesibo_module_t` structure pointer as the first argument
- Pass or Consume mechanism according to return value: Each function returns an integer value which is defined in the file [module.h] 
`MESIBO_RESULT_PASS` pass the data as it is and the recipient is notified

`MESIBO_RESULT_CONSUMED` where the data is consumed and the recipient is not notified of this data



Let's look in detail at the different callback functions and their prototypes:

### on_message 
This function is called when the module recieves a message.
```C
int (*on_message)(mesibo_module_t *mod, mesibo_message_params_t *params, const char *message, mesibo_uint_t len)
```
Prameters:
1. `mod` of type `mesibo_module_t*` - Pointer to mesibo module struct
2. `params` of type `mesibo_message_params_t*` Pointer to message params struct.It contains message parameters such as 
`id`- Unique message identifier(For example it can be a psuedo-random number), `from`-  sender of the message, `to`-  message recipient,etc For more details refer [Data Structures]().
3. `message` of type character buffer `const char*` which contains the message data bytes
4. `len` of type `mesibo_uint_t` containing the length of the message ie; number of bytes in the data buffer

Returns:

`MESIBO_RESULT_CONSUMED` if message data is to be consumed and the recipient will not recieve THIS message data.

`MESIBO_RESULT_PASS` pass the message data and parameters as it is to the recipient

### on_message_status
This function is called when message is sent from the module and you recieve the status of the message.
```C
int (*on_message_status)(mesibo_module_t *mod, mesibo_message_params_t *params, mesibo_uint_t  status)
```
Prameters:
1. `mod` of type `mesibo_module_t*` - Pointer to mesibo module struct
2. `params` of type `mesibo_message_params_t*` Pointer to message params struct.It contains message parameters such as 
`id`- Unique message identifier(For example it can be a psuedo-random number), `from`-  sender of the message, `to`-  message recipient,etc For more details refer [Data Structures]().
3. `status` of type `mesibo_uint_t` containing the status of the sent message which corresponds to different [status codes](https://mesibo.com/documentation/api/real-time-api/data-structures/#messageparams) such as `MSGSTATUS_SENT`,`MSGSTATUS_DELIVERED`,`MSGSTATUS_READ`,etc

Returns:

`MESIBO_RESULT_CONSUMED` if message data is to be consumed and the recipient will not be notified of THIS status data.

`MESIBO_RESULT_PASS` pass the status data and parameters as it is to the recipient

### on_cleanup
This function is called when the module process is complete and to cleanup.
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
##  Core API functions
These functions are initialised by Mesibo and you can utilise these functions to send a message, send a http request ,print logs,etc. Please note, that unlike the callback functions you need not pass any function references to initialise these functions as they are internally defined and initialised by Mesibo.

Let's look at the syntax of these functions.

### send_message
This function can be used to send a message from on end-point or user to another.
```C
int (*send_message)(mesibo_module_t *mod, mesibo_message_params_t *params, const char *message, mesibo_uint_t len);
```
Parameters:
1. `mod` of type `mesibo_module_t*` - Pointer to mesibo module struct
2. `params` of type `mesibo_message_params_t*` Pointer to message params struct.It contains message parameters such as 
`id`- Unique message identifier(For example it can be a psuedo-random number), `from`-  sender of the message, `to`-  message recipient,etc For more details refer [Data Structures]().
3. `message` of type character buffer `const char*` which contains the message data bytes
4. `len` of type `mesibo_uint_t` containing the length of the message ie; number of bytes in the data buffer

Returns:
Integer : 0 on Success , -1 on failure

### http
This function can be used to send a http request (REST API type)
```C
int (*http)(mesibo_module_t *mod, const char *url, const char *post, mesibo_module_http_data_callback_t cb, void *cbdata, module_http_option_t *opt)
```
Parameters:
1. `mod` of type `mesibo_module_t*` - Pointer to mesibo module struct
2. `url` is a string which contains the path of the request, e.g. "api.mesibo.com/api.php"
3. `post` is a string which contains the POST data .For example the part of the request after the question mark (e.g. "op=getcontacts"
4. `cb` is the call back function pointer whose prototype should match `mesibo_module_http_data_callback_t`. You will get the response of your http request, asynchronously through this callback function. 
5. `cbdata` is a pointer to data of arbitrary user defined type. This callback data is passed on to your callback functionthat you have passed in the previous argument.
6. `opt` is the `module_http_option_t` structure which contains `options` or additional parameters that you need to pass in your http request such as extra_header,content_type,etc. For more details about the `module_http_option_t` structure, refer [Data Structures]()

Returns:
Integer : 0 on Success , -1 on failure

### log
This function can be used to print to mesibo container logs.
```C
int (*log)(mesibo_module_t *mod, mesibo_uint_t level, const char *format, ...)
```
Parameters:
1. `mod` of type `mesibo_module_t*` - Pointer to mesibo module struct
2. `level` of type `mesibo_uint_t` which defines log level
3. `format` format string for printing data which is similar to that of `printf` followed by the data to print.

Returns:
Integer : 0 on Success , -1 on failure













