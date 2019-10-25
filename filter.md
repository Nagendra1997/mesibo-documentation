# Filter Module

This repository contains the source code for Filter Module. This is an example of a simple profinity filter to drop messages containing profanity. You can download the source and compile it to obtain the module- a shared library file. Also, you can load the pre-compiled module which is provided as `mesibo_mod_filter.so`

Refer to the [Skeleton Module](https://github.com/Nagendra1997/mesibo-documentation/blob/master/skeleton.md) for a basic understanding of how Mesibo Modules work.The complete documentation for Mesibo Modules is available [here](https://mesibo.com/documentation/loadable-modules/)

## Overview of Filter Module
- A list of words defined as profinity or blocked words is provided in the module configuration (In the file `mesibo.conf`).
- In module initialisation, the profanity list is obtained from the configuration items list and the callback function reference `filter_on_message` is provided as `m->on_message = filter_on_message`
- When a message is exhanged betweeen users, Mesibo invokes the `filter_on_message` callback function of the module with the message data and it’s associated message parameters such as sender, expiry, flags, etc.
- The filter module analyzes the message to find any profanity or objectionable content and returns whether the message is safe or not.
- If no profanity was found, `filter_on_message` returns `MESIBO_RESULT_PASS` and the message is safely sent to the recipient. . If the message is found to contain profinity `filter_on_message` returns `MESIBO_RESULT_CONSUME` and the unsafe message is dropped and prevented from reaching the receiver.
