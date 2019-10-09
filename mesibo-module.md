# Dynamically Loadable Modules and Scripting

Mesibo On-Premise is designed by developers for developers. 
Its dynamically loadable module architecture lets you load your own code for deeper integration with your infrastructure, 
enabling you for unlimited creative possibilities. 

This makes Mesibo, the most compelling real-time communication platform existing today.

## Overview
0. Prerequisites
1. Mesibo Module and Service architecture
2. Module process overview
3. Anatomy of Mesibo Module
   a. Module Configuration Struct
   b. Module initialisation
   c. Callback Functions
   d. Core API functions (Better name)
4. Writing and Compiling Mesibo Modules 
5. Loading modules
5. Code references and Examples

## Prerequisites 
- Running Mesibo On Premise
- Getting Started Guide
- Knowledge of Building and deploying C/C++ shared libraries
- Request and Callback mechanism

## Mesibo Module and Service architecture 
- Where module resides and loads
- A bridge between your library-Service-Script
<diagram>

## Module process overview
Mesibo module is made of two components:
1. Core functions
2. Callback functions

Typical processing cycle:
Client sends message → Mesibo chooses the appropriate function/process to launch as defined in your module interface→Processing through Module Chain →Call custom script interface→Return Response via callback→ Choose whether to PASS or CONSUME result → Send response to client
<diagram>

## Anatomy of Mesibo Module

### a. Module Configuration Struct
A Mesibo Module is described by a structure defined below. The mesibo module configuration contains:

`version` Model Version
`flags`
`name` Name of your module
`signature`

There are a set of callback functions corresponding to different events:
`on_message` When a message is generated and routed through your module
`on_messagestatus` When a message is sent and status update


```
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





