---
description: Enable applications with conference calling, live streaming, screen sharing, group chat and more
keywords: messaging platform, chat api platform, voice, video calling, mesibo communication platform
title: Mesibo Live Conferencing and Streaming Platform- Conference Calling, Live Streaming, Screen sharing and chat API
---

Mesibo Live Conferencing and Streaming Platform helps you  build applications at scale for teleconferencing, virtual events and webinars, on-demand streaming and more - all at no additional cost with an on-premise platform!

This article consists of two parts. In the first part, we will understand how group calling works by simply extending the concept of group messaging. In the next part, we will build a fully functional Zoom like conferencing application. You can find a live demo [here](https://mesibo.com/livedemo) and the source code [here](https://github.com/mesibo) 

> **Disclaimer** : The Live Conferencing and Streaming APIs are currently under development for more platforms and will be continously updated. Please ensure that you are using the latest version of Mesibo APIs and refering to updated documentation

# How do Mesibo Live Platform APIs work?
Mesibo has made it extremely simple to use and build with group calling and streaming APIs. Mesibo conferencing and streaming can be readily integrated with your existing applications which are using Mesibo messaging and call APIs.

Mesibo APIs for conferencing, simply extend the core features offered by mesibo [Group Management and Messaging APIs](https://mesibo.com/documentation/api/backend-api/#group-management-apis). So, there is absolutely no need to learn anything additional.

If you are not familiar with using [Mesibo Group Management APIs](https://mesibo.com/documentation/api/backend-api/#group-management-apis), here's a little recap. Feel free to skip ahead if you are already familiar with group messaging.
![group-messaging-calling](grp-msg-vs-call.jpg)

## Group Messaging
Mesibo allows you to create groups having a set of users as group members. Once you create a group, you can send messages to the group, and all the group members will receive the messages. To set up a group with Mesibo APIs you follow the steps below:

1. Create a group.
2. Add Members.
3. Grant Permissions about who can send and received messages. For example you can set permissions such that only members can send, only selected members can send, only Selected Members can receive, etc. You can also grant admin rights to who can change group settings, who can remove members, etc

## Extending Group Messaging to Group Calling
In group messaging, when you send a message to the common group every member gets a message notification. The user opens the message and reads it. In the same way, in group calling, when you make a call to the group, every member of the group get's a call notification and each member of the group will connect with the call. 

So, for live conferencing and streaming there is a small addition to setting up a group.

1. Set up a group
2. Get list of members  
2. Place a call to the group   
4. Connect to each member of the group over voice and video.

It is that simple!

Remember how you can specify member behaviour in case of group messaging? You can restrict which member can send a message,who can only recieve message, etc. By specifying member behaviour and permissions you can build groups of different [types](https://mesibo.com/documentation/api/backend-api/#group-management-apis). Using the very same group settings you can build any type of conferencing and streaming app that you need.

![streaming-scenarios](live.jpg)

- **Conferencing**: A many-to-many group. All members of the group can publish their own stream and see other's streams. There can also be admins or moderators who have special permissions to change group settings, remove participants, mute members, etc. Apps like Zoom, Google Meet,etc are examples of this.

- **Live Streaming**: A one-to-many group. Only one person has been granted the permission to publish. All other members can only receive the stream ie; They are just listeners. An online webinar, a live class, are examples of such a scenario.

- **On-Demand Streaming** You can upload media to the group, which only members can view on-demand, whenever they need it. You can think of Youtube and Netflix as an example of such applications.

Let's now take a look at the different API functions that you can use to set up your conferencing and streaming platorm.
A detailed documentaion of each function/method can be found [here] and will be explained with examples in the next section 

### Creating a group
Use Mesibo's Group Management APIs to create a group.Add members and set permissions of the group members.

### Get list of members
When any member joins the group, they will be getting a list of other members in the group. You will be getting the list through the callback function `Mesibo_onParticipant`

### Initializing Group Call
To initialize a group call, you need to use `initGroupCall()` which will provide you with a group call object. Now you can link that group call object with a group-id.

### Placing a call to the group
To place a call to the group, you first need to set up your own local stream. To do this you need to call `getLocalParticipant()` and pass your name and address as arguments.
Then, you can place a `call` to the group.

### Connecting to other streams
Once you get a list of participants, you can choose to connect to each of those streams. To do this you can place a `call` to the selcted participant 


## Fundamentals of Group Calling and Streaming

 There are three basic components to the conferencing and streaming system:
 1. **Publisher** : Your own stream, where you publish your self through a camera stream or share screen
 2. **Participants**  : A list of members whom you can subscribe to.
 3. **Streaming Backend**: Mesibo's real time backend collects and delivers various streams from remote endpoints to you.
 
Let's now take a look at how all of this is built on top of Mesibo's User and Group Management APIs.

### Publisher
You are the publisher. As a member of the conference room group you can stream your own self and send messages to other members. The publisher is a Mesibo User with an access token and is a member of the group - conference room.

### Participants
Other members, are also mesibo users who are part of the same group(conference room) as you(the publisher). Other group members are also publishing their own streams.

### Subscribing to Streams
Once you join a room, you get a list of other group members through the callback function `Mesibo_onParticipant`. You can choose and subscribe to the stream of each member to view it. Mesibo will be listening for any members leaving or joining the group and will inform you through appropriate callback functions.

### Unlimited Participants
There is no limit to the number of groups that you can create with Mesibo. Or the number of participants a group can have.
So, unlike other streaming platforms or services, Mesibo does not impose any limit on the total number of participants in a single group call.

> **Note**: The number of participants you can stream simulataneously is limited by your CPU capacity and not by an arbitrary limit imposed by Mesibo APIs

## Mesibo Live Demo
We have provided a sample web application which contains examples for using Mesibo Conferencing and Live Streaming APIs, to build various features required for a functional video conferencing app. The current demo is built using Mesibo Javascript SDK. Conferencing and Streaming APIs for other platforms are also currently under development and will be made available soon.

## Conference Room
The conference room is fundamentally a group - with a group name and group id. You can [create a group](https://mesibo.com/documentation/api/backend-api/#create-a-group), [add and remove members](https://mesibo.com/documentation/api/backend-api/#add-or-remove-group-members), with all the features of a Mesibo Group like sending messages and files,etc.

### Privacy & Security
You can also have room pin set up for privacy along with say something like a waiting room, where only members you approve of can join the room.

You can dynamically change the group or the member behavior at any point in time and it will be instantly applied to the group or group member(s).

## Creating & Joining a Room

## Setup Mesibo Group Calling

### Install Mesibo Javascript SDK
The easiest way to [install Mesibo Javascript SDK](https://mesibo.com/documentation/install/javascript/) is to just include following in your code:

<script type="text/javascript" src="https://api.mesibo.com/mesibo.js"></script>

You must use a secure website (https) to use mesibo javascript. It may NOT work from http:// or file:// sites due to browser security restrictions.

### Initialize and Run Mesibo
To initialize Mesibo, create an instance of Mesibo API class `Mesibo`. 
 For example, you can initialize and run mesibo as follows:
 
```javascript
$scope.initMesibo = function(){

    $scope.mesibo = new Mesibo();
    $scope.mesibo.setAppName(MESIBO_APP_ID);
    if( false == $scope.mesibo.setCredentials(MESIBO_ACCESS_TOKEN))
        return -1;

    $scope.mesibo.setListener($scope);
    $scope.mesibo.setDatabase("mesibo");
    $scope.mesibo.start();

    $scope.initializeStreaming();
    
    return 0;
}
```
### Initialize Group Calling & Streaming

After initializing Mesibo, To set up group calling and streaming call the `initGroupCall` method to create the group call object.

Call the `getLocalParticipant` method to initialize publisher(your self stream) 

An example in javascript is as follows,
```javascript
$scope.initializeStreaming = function() {

    //Create group call object
    $scope.live = $scope.mesibo.initGroupCall(); 
    
    //Set Group ID
    $scope.live.setRoom($scope.room.gid); 
    
    // Create a local stream object, Set Publisher name and address
    $scope.publisher = $scope.live.getLocalParticipant($scope.user.name, $scope.user.address); 
    if(!isValid($scope.publisher))
        	return -1; 
    
    return 0;
}          
```
### Publishing your self stream
```javascript
    $scope.publish = function() {
	var o = {};
	o.peer = 0;
	o.name = '';
	o.groupid = $scope.room.gid;	
	o.source = $scope.toggle_source;	   
	
        $scope.publisher.call(o, 'video-publisher', $scope.on_stream, $scope.on_status);
}
```

## Callback Functions
Various callback functions are called for different events such as when a participant enters the room, 


