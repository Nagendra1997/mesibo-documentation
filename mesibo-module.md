# Dynamically Loadable Modules and Scripting

Mesibo On-Premise is designed by developers for developers. 
Its dynamically loadable module architecture lets you load your own code for deeper integration with your infrastructure, 
enabling you for unlimited creative possibilities. 

This makes Mesibo, the most compelling real-time communication platform existing today.
<add more>

## Overview
0. Prerequisites
1. Mesibo Module and Service architecture
2. Module process overview
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

## Mesibo Module and Service architecture 
- Where module resides and loads
- A bridge between your library-Service-Script
<diagram>
   <Explain>

## Module process overview
Mesibo module is made of two components:
1. Core functions
2. Callback functions

Typical processing cycle:
Client sends message → Mesibo chooses the appropriate function/process to launch as defined in your module interface→Processing through Module Chain →Call custom script interface→Return Response via callback→ Choose whether to PASS or CONSUME result → Send response to client
<diagram>
   <Explain>

## Anatomy of Mesibo Module

### a. Module Configuration Struct
A Mesibo Module is described by a structure defined below. This is one of the most important data structures used in Mesibo module. 
The mesibo module configuration contains:

`version`: Model Version which is of type `mesibo_uint_t`

`flags`: 

`name`: Name of your module which is a string 

`signature`:

These attributes need to be initialised in the init function which we shall study later.

There are a set of callback functions corresponding to different events which needs to be initialised by the caller of the module. Callback functions that you provide should have the exact same signature as defined in the `mesibo_module_t` struct.
For a detailed explanation of callback functions and their prototypes refer [Callback functions]

`on_message`: When a message is sent from on endpoint to another in your application 

`on_messagestatus` : When a message is sent and you will recieve a status update

`on_call`: When a call is recieved

`on_call_status`: When a call status is recieved

`cleanup` When module completes its work and is unloaded

There are functions which are initialised by Mesibo which you can use. For a detailed explanation  refer [Core API functions]

`send_message` To send message from one user to another

`http` To send http POST request

`log` To print logs to mesibo server logs

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
### b. Module initialisation
The above module structure initialisation is performed by a callback function `mesibo_module_init_fn` the prototype for which can  be found in the file [module.h]().This function is automatically called by mesibo when module is constructed. The naming convention for this function is `mesibo_module_<module name>_init`

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

### c. Callback Functions
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


















