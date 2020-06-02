---
description: Enable applications with conference calling, live streaming, screen sharing, group chat and more
keywords: messaging platform, chat api platform, voice, video calling, mesibo communication platform
title: Mesibo Live Streaming Platform- Conference Calling, Live Streaming, Screen sharing and chat API
---

Mesibo Streaming Platform offers a plethora of capabilities and features to build applications for live streaming, conferencing calling integrated with group chat, one-to-one messaging, one-to-one calls and more. With Mesibo's APIs for group calling, chat and streaming you can build streaming applications at scale, with high quality and low latency.

> **Disclaimer** : The conferencing and Livestreaming APIs are currently under development for more platforms and will be continously updated. Please ensure that you are using the latest version of Mesibo APIs and refering to updated documentation

# How Mesibo Group Calling or Conferencing works?
Mesibo has made it extremely simple to use and build with group calling and streaming APIs. There is no need to learn anything additional or restructure your applicaton. With a single fundamental concept of a group, you can extend and build all the required features for your streaming or conferencing app.

If you are not already familiar with Mesibo's Group Management API [refer](https://mesibo.com/documentation/api/backend-api/#group-management-apis). 

## Fundamentals of Group Calling and Streaming

 There are three basic components to the conferencing and streaming system:
 1. **Publisher** : Your own stream, where you publish your self through a camera stream or share screen
 2. **Participants**  : Streams of others who are publishing, whom you can subscribe to.
 3. **Streaming Backend**: Mesibo's real time backend collects and delivers various streams from remote endpoints to you.
 
Let's now take a look at how all of this is built on top of Mesibo's User and Group Management APIs.

## Conference Room
The conference room is fundamentally a group - with a group name and group id. You can [create a group](https://mesibo.com/documentation/api/backend-api/#create-a-group), [add and remove members](https://mesibo.com/documentation/api/backend-api/#add-or-remove-group-members), with all the features of a Mesibo Group like sending messages and files,etc.

You can build various types of rooms and additional features for conferencing with the same set of [group management apis](https://mesibo.com/documentation/api/backend-api/#group-management-apis)

For example,
- **Webinar**: One-to-many broadcast, a single publisher and muliple members. Admin can mute all and have additional privelages in `Javascript` y
- **Private Rooms**: Only invited/selecteed room members can join and publish to a group. 
- **Public Rooms**: Anyone can join and publish to group.

### Privacy & Security
You can also have room pin set up for privacy along with say something like a waiting room, where only members you approve of can join the room.

You can dynamically change the group or the member behavior at any point in time and it will be instantly applied to the group or group member(s).

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


