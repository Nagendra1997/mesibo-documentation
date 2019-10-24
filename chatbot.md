---
description: Mesibo Loadable Modules
keywords: open, source, on-premise, messaging, chat, voice, video
title: Mesibo Loadable Modules
---

### Sample Chatbot Module
It is extremely simple to get started with Mesibo to build chatbots, which can integrate powerful analytical abilities in speech, image recognition, Natural Language processing, etc in your backend using loadable modules. You can interface with any tool or library of your choice such as Dialogflow, IBM Watson, Tensorflow, etc using REST endpoints.

![Module Chatbot Sample](module_chat_bot.jpg)

Let's look at how you can build a chatbot using Mesibo Module:
- When you receive a message, you get the message text via the callback function `on_message` with the message data and it's associated message parameters as arguments. This message is the `query` to your chatbot.
- Now, you can create a chatbot module function that takes this message data/ text as input and sends it to a REST endpoint as HTTP Post data which contains the query using the function `http`. 
- In the function [http](),You pass the REST endpoint `url`, `post` data -which is as per the query format of your REST endpoint , callback function (where you will receive the response to your HTTP request) and Call back data (which is passed along to your callback function), `options` which will contain additional headers such as Authorisation header,etc (If required)
- Now based on your request, your chatbot model will process the query and send the appropriate response.
- In your callback function, you will receive the HTTP response for your query in the form of a JSON string. Send the  response using  `send_message` function.

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
