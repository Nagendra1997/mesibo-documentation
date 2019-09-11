---
title: Write your First mesibo Enabled Application - C/C++
description: Write your First mesibo Enabled Application - C/C++
keywords: mesibo, android, ios, cpp, c++, c
---
{% include_relative nav.html selected="cpp" %}

In this part, we will create a simple real-time app using Mesibo C/C++ API. The Mesibo C/C++ SDK allows you to create real-time application on `Linux`, `Mac OS`, and `Raspberry Pi` platforms. Mesio C/C++ API is the low-level API which is extremely powerful, efficient, and light-weight.

Mesibo C/C++ library is available as a shared library(libmesibo.so) which can be seamlessly integrated into any application by compile time linking or by loading it dynamically. You can even use it from other languages of your choice like Python, PHP, Matlab, etc. as all of these languages allow interfacing with C/C++ libraries. 

### Supported Platforms
- CentOS / RedHat 7.x or above
- Debian / Ubuntu
- Mac OS
- Raspberry Pi


### Prerequisites

- Installed the Mesibo Real-Time C/C++ Library. If not, refer installation instructions [here](/documentation/install/linux/) 

- Read the [Preparation Guide](/documentation/tutorials/first-app/)

- Read the [Anatomy of a Mesibo Application](/documentation/tutorials/first-app/anatomy/) 

- Basic knowledge of writing and compiling C/C++ code

### First C/C++ App

Now let’s quickly start coding:

1. Open a code editor
2. include `mesibo.h` in your code
3. Create a class derived from the `CMesiboNotify` class to listen to all the message and call events. 
4. Initialize mesibo using the user token and listener.

```cpp
#include <mesibo.h>
```

Below is Mesibo Event Listener

```cpp
class CNotify : public CMesiboNotify {
	public:

	// You will receive the connection status here
	int on_status(int status, uint32_t substatus, uint8_t channel,
			const char *from) {
		ERRORLOG("===> on_status: %u %u\n", status, substatus);
		return 0;
	}


	// Invoked on receiving a new message or reading database messages
	// You will receive messages here.
	int on_message(tMessageParams * p, const char *from, const char *data,
			uint32_t len) {
		ERRORLOG(
				
			"===> test app message received: uid %u status %d channel %d type %u "
			"id %" PRIx64 " refid %lu groupid %u, when %" PRIu64
			" from %s, flag: %x len %d: %.*s\n",
			p->uid, p->status, p->channel, p->type, p->id, p->refid, p->groupid,
			p->when, from, p->flag, len, printlen, data);

		return 0;

	}

	// Invoked when the status of the outgoing or sent message is changed
	// You will receive the status of sent messages here
	int on_messagestatus(tMessageParams * p, const char *from, int last) {
		ERRORLOG(
			"===> on_messagestatus status %u id %u when %u ms (%u %u) from: %s\n",
			p->status, p->id, m_api->timestamp() - p->when, m_api->timestamp(),
			p->when, from ? from : "");
		return 0;
	}
};

```
 
Following is the Mesibo Initialization code.

```cpp
void mesibo_init(){
	
	// Create a Mesibo Instance
	IMesibo *m_api = query_mesibo("/tmp");  

	// Add Listener
	CNotify *n = new CNotify();
	m_api->set_notify(0, n, 1);

	// Set your AUTH_TOKEN obtained from the Mesibo Console
	m_api->set_credentials("aea59d3713701704bed9fd5952d9419ba8c4209a335e664ef2g");

	// set the name of the database
	if (0 != m_api->set_database("mesibo.db")) {
		fprintf(stderr, "Database failed\n");
		return -1;
	}

	// Set  APP_ID which you used to create AUTH_TOKEN 
	m_api->set_device(1, "MyDeviceId", appid, "1.0.0");

	// start mesibo
	m_api->start();

	return 0;
}

```

That’s it - you are now ready to receive your first real-time message.

## Compiling and Running sample C/C++ application

It is recommended that you use a modern C/C++ compiler such as gcc (GCC 4.x or above) or clang. You can compile your code by linking the Mesibo Library as shown below:

```bash
g++ my_first_app.cpp -o myfirstapp -lmesibo
```

Run the output executable

```bash
./myfirstapp
```

2. `on_status` should cycle through various status information. Finally, you should receive status=1 which indicates that your app is successfully connected to the mesibo real-time server and ready to send and receive real-time messages.

3. Since we do not have any other users right now, we will use **mesibo console** to send a test message. In a later section, we will learn how to send messages from the code itself.

- Go to **Console ->Application->Users**. You should see the user you have created.

- Go to user details by clicking the `Edit` button. Scroll down, you will see a section to `Message User`
Enter 1000 (or anything) in `From` field, check `Create This User` checkbox, type message and click on `Send`.

4. You will instantly receive this message in your console/terminal in `on_message` listener.

## Sending Messages
In the previous section, we have used mesibo console to send a message. Now we will quickly learn how to send messages from the code itself. To send messages, we will use `message` real-time API for which we will need destination user, message-id and the message itself.

Invoke the following function anywhere from your code to send a text message. 

```cpp
int send_text_message(IMesibo* m_api,const char* to,const char * message){

	tMessageParams p = {};
	p.id = m_api->random32();
	p.expiry = 3600;

	m_api->message(&p, to, message, strlen(message));
}

```

That’s it! Try it out by creating two users and send messages to each other by using the above function.
