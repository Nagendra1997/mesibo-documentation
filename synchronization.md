---
title: "Get Started | Synchronization" 
keywords: mesibo, android, ios
description: Synchronizing messages & user data 
heading: "Get Started: Synchronization" 
---
{% include_relative nav.html selected="synchronization" %}

In this part, we will learn about synchronizing messages and user data on multiple devices with mesibo. 
> Note that the synchronization feature is exclusive to our [On-Premise]({{'/on-premise'}}) offering, where you can route all calls & messages through your servers. 

## Prerequisites
You MUST go through the following prerequisites before you read further.

- Read the [First App]({{ '/documentation/tutorials/get-started/first-app/' | relative_url }}) Guide.
- Read one of the Android, iOS, JS, etc of this tutorial.
- Get familiar with [Read APIs]({{ '/documentation/tutorials/get-started/reading-messages' | relative_url }})
- Run [Mesibo On-Premise](https://mesibo.com/on-premise). See [On-Premise Docs]({{ '/documentation/on-premise' | relative_url }}) for installation instructions and more.

## Multi-Device Synchronization 
Mesibo stores your app's messages on a local database on the user's device. To display messages, you need to read messages(Using [Read APIs]({{ '/documentation/tutorials/get-started/reading-messages/#how-to-read-messages' | relative_url }})) from this local database. Now, if the user moves to a new device you will have a brand new database. Your user cannot view the old messages by reading the new database. The messages & other data present in the old database, stored on the old device is not available to the new device. But, if you are using [Mesibo On-Premise]({{ '/documentation/on-premise' | relative_url }}) you can always store messages and call data on your server. So, whenever a user moves to a new device you can synchronize with your server and get the required messages. To perform this on-demand synchronization, mesibo provides [sync APIs](#using-the-sync-api). 
![Device Sync](images/device-sync.png)

## Reading Messages 
When you use the `read` API you will be accessing the local database stored on the user's device. For example, in Android to read messages sent/received from a user with the address `"dest"` you call read as follows:
```java
Mesibo.ReadDbSession mReadSession = new Mesibo.ReadDbSession("dest", 0, null, this);
int count = mReadSession.read(100);
```
Once you call `read()`, it reads the local database and checks if there are messages for the user called `"dest"`. It will return the number of messages and you will be getting so many messages for that user through the listener `Mesibo_OnMessage`. See the section on [Read APIs]({{ '/documentation/tutorials/get-started/reading-messages' | relative_url }}) to know more.  

But when your user logs in on a new device, and you call `read()` for the first time, it will not give you any messages ie; The `count` returned will be `0`. This is because the new database will be initially empty and may not contain any messages for the user you have attempted to `read`. You need to sync the database on the new device to include messages for the selected user/group present on your server. One approach to do this will be, to transfer all of the data from your on-premise server's database to the app's local database. This is not recommended. You only need to `sync` messages for the selected user on demand and you can easily achieve this by using mesibo's `sync` API.

## Using the sync API
When your call to `read()` returns zero messages(which means there are no messages available in the local database for the user you are calling read), you may request for a `sync`. The `sync` method takes two parameters:
- **count** The number of messages you want to synchronize
- **OnSync** A callback function that will be called when sync completes. 

Note that a call to `read()` is synchronous while the call to `sync()` is asynchronous. The result of your `sync`
the request will be available through the listener `on_sync`. The call to `sync` checks messages stored on the mesibo server and transfers those messages to your local database. The number of messages that have been synced will be available as a parameter `count` in the `on_sync` listener. You can read `count` number of messages using `read()`.

## Restoring Messages on a new device by synchronization

When a user logs in with a new device, you have a new local database that you need to update with the messages stored on the server's database. That is, you synchronize the user’s local database from the server database. However, you need not pull all the data from the server's database. You only have a single app user on the device. Whenever your app user needs some data which is not already present on the local database, only then there is a need to sync from the server. 

For example, let's say you are logged in as `User-A` on your app and you need to display the chat history between you - `User-A` and another `User-B`. To do this you will use the `read` API to read messages for the `User-B`. If you are on a new device, and you have never exchanged messages with `User-B` on this device, the `read` API will not return any messages. You have nothing to display! But, if you had chatted with `User-B` earlier on a previous device, your messages will be stored on the server database. You need to access these messages present on the **server database** and restore them onto the **local database**. This is what the `sync` API does. Once the sync is complete, you can then `read` these old messages from the local database. 

Here are some code examples, demonstrating how you can restore messages, by synchronizing messages for a selected user or group. 

In Android, 
```java
int c = mReadSession.read(100);
if(c == 0){
	// No messages available for this user in local database
	// Synchronize from server and check again
	// Sync for say 100 messages
	mReadSession.sync(100, this);
}

@Override
public void Mesibo_onSync(int i) {
	// i number of messages have been synced
	// You can now read i messages
	if(i <= 0) return;
	mReadSession.read(i);
}
```
In iOS,
```ios


```

In Javascript, after calling read you will get the messages when the callback `on_messages` is called. You can then call `getMessages()` to get your array of messages. If this array is empty, you can then call `sync`. When `sync` is complete, the `on_sync` callback will be called where you can then call `read()`.  

> The way `read` works in the mesibo Javascript API is differs slightly from Android/iOS APIs. This is mainly because of how the local database(IndexDB) operates. In Android/iOS read messages are received in the `onMessage` callback. In Javascript, you get the read messages through a read handler. 

```javascript
let rSession =  mesibo.readDbSession(peer, groupid, null,
	function on_read(result) {
		if(0 == result){
			// No messages to read in the local database
			// Sync for say 100 messages
			this.sync(100,
				function on_sync(i){
					if(i > 0){
						this.read(i);
					}
			});
			return;
		}

		let msgs = this.getMessages();
		// Display messages
	});
rSession.read(100);
```
In the next section, we will explain the various steps involved in On-Demand Synchronization. In the examples, above we have seen how sync is useful when restoring messages when a user moves to a new device and the database on the new device is empty. With on-demand synchronization, instead of syncing a lot of messages in one-shot which may be time-consuming, you can do it on a need basis. For example, when a user scrolls and you have to load more messages, you can sync & read additional messages on-demand.

## On-Demand Synchronization 
![Sync Flow](images/flow-sync.png)

To display messages for a selected user or group, you need to read the message history. To do this, you need to follow the steps below:

1. Create a read session object for a selected user or group. 
```java
Mesibo.ReadDbSession mReadSession = new Mesibo.ReadDbSession("selected_user", 0, null, this);
```
2. Call `read` by specifying the number of messages you want to read from the local database. Let's say we want to display 10 messages. 
```java
int readCount = 10; 
int result = mReadSession.read(readCount);
```
3. If the `readCount` that you are requesting is less than the number of messages available in the database(for that user/group), it means you have sufficient messages in your local database. For example, if you tried to read 10 messages and you have 10 messages, it's all good. `read()` will return 10. You can display 10 messages on the screen. 

4. Now, if the user scrolls to the top of the chat and wants to view older messages we need to load more messages from the database. Let's say we want to load 10 more messages from the database. We call read again. Remember, we have previously read 10 messages from the database.  

If you again try to read 10 messages, there are two possibilities:

A) **There are sufficient messages in your database**. If there are 10 more messages in the database for the selected user/group, read() will again return 10 and we will continue to display 10 more messages on the screen.

B) **You have run out of messages in your database**. If 10 more messages are NOT available for the selected user or group, read will return a value <10. ie; Less than your requested `readCount`.

In the second case, where you have run out of messages on your database you need to synchronize more messages from your server. Say, `read` returned 4 messages. You need 6 more messages. You can display 4 messages, and asynchronously request 6 more messages from your server using `sync`.

> Note: If your database is empty(For example: when the user moves to a new device), `read` will return 0 which will be less than the number of messages you tried to read. So, you have the second case where you try to synchronize from your server. 

```java
int readCount = 10;
int result = mReadSession.read(readCount)
if(result < readCount)
	mReadSession.sync(readCount - result, this);
```
5. If there are older messages available on the server database, for the user/group you have requested `sync` will give you the number of messages you requested for. In our example, we requested 6 older messages. Once `sync` completes, your local database will be updated with 6(or lesser) older messages. The number of messages synced will be available in the callback that you passed to `sync`.

You can read these messages and display it now.
```java 
@Override
public void Mesibo_onSync(int i) {
	// i number of messages have been synced
	// You can now read i messages
	if(i <= 0) return;
	mReadSession.read(i);
}
```

