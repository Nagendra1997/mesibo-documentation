---
description: Enable applications with conference calling, live streaming, screen sharing, group chat and more
keywords: messaging platform, chat api platform, voice, video calling, mesibo communication platform
title: Mesibo Live Streaming Platform- Conference Calling, Live Streaming, Screen sharing and chat API
---

Mesibo Streaming Platform offers a plethora of capabilities and features to build applications for live streaming, conferencing calling integrated with group chat, one-to-one messaging, one-to-one calls and more. With Mesibo's APIs for group calling, chat and streaming you can build streaming applications at scale, with high quality and low latency.

> Disclaimer : The conferencing and Livestreaming APIs are currently under development for more platforms and will be continously updated. Please ensure that you are using the latest version of Mesibo APIs and refering to updated documentation

### How Mesibo Group Calling or Conferencing works?
Mesibo has made it extremely simple to use and build with group calling and streaming APIs. There is no need to learn anything additional or restructure your applicaton. With a single fundamental concept of a group, you can extend and build all the required features for your streaming or conferencing app.

If you are not already familiar with Mesibo's Group Management API [refer](https://mesibo.com/documentation/api/backend-api/#group-management-apis). 

## Fundamentals of Group Calling and Streaming

 There are three components of any group calling system:
 1. Publisher : Your own stream, where you publish your camera stream or share screen
 2. Participants Stream : Streams of others who are publishing, whom you can subscribe to.
 3. Streaming Backend: Mesibo's real time backend that collects and delivers various streams from remote endpoints to you.

Let's now take a look how all of these features are built on top of Mesibo's User and Group Management APIs.

### Publisher

### Unlimited Participants
There is no limit to the number of groups that you can create with Mesibo. Or the number of participants a group can have.
So, unlike other streaming platforms or services, mesibo does not impose any limit on the total number of participants in a single group call.

> Note: The number of participants you can stream simulataneously is limited by your CPU capacity and not by an arbitrary limit imposed by mesibo APIs


Let's take a look at the different callbacks and functions of the mesibo streaming API

## Setup Mesibo Group Calling

### Install Mesibo Javascript SDK
The easiest way to [install Mesibo Javascript SDK](https://mesibo.com/documentation/install/javascript/) is to just include following in your code:

<script type="text/javascript" src="https://api.mesibo.com/mesibo.js"></script>

You must use a secure website (https) to use mesibo javascript. It may NOT work from http:// or file:// sites due to browser security restrictions.

### Initialize and Run Mesibo
To initialize Mesibo, create an instance of Mesibo create and instance of Mesibo API class `Mesibo`. 
 For example, in `Javascript` you can initialize and run mesibo as follows:
 
```javascript
$scope.initMesibo = function(){
		$scope.mesibo = new Mesibo();
   	$scope.mesibo.setAppName(MESIBO_APP_ID);
		if( false == $scope.mesibo.setCredentials($scope.user.token))
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
}          
```
## Callback Functions
Various callback functions are called for different events such as when a participant enters the room, 


