---
description: Enable applications with conference calling, live streaming, screen sharing, group chat and more
keywords: messaging platform, chat api platform, voice, video calling, mesibo communication platform
title: Mesibo Conferencing and Streaming Platform- Conference Calling, Live Streaming, Screen sharing and Chat API
---

Mesibo Conferencing and Streaming Platform helps you build applications at scale for teleconferencing, virtual events, webinars, on-demand streaming, and more. The platform is available as native and web based offerings, that can be used both on the cloud and on-premise at no additional cost.

Mesibo offers a powerful combination of features to build any kind of conferencing and streaming application with minimal setup and cost.

## Why mesibo for conferencing?
- Just under 700KB footprint makes mesibo, the lightest conferencing server in the world. You can run mesibo's conferencing platform on any server with minimum resources.

- Capacity on Demand - Elastic architecture lets you use Kubernetes or Docker Swarm orchestration to scale-up or scale-down capacity on demand!

- Simultaneous publishing to multiple groups - this unique mesibo capability enables you to make interesting use cases for live-streaming.
Standard, HD, Full-HD, and 4K conference rooms - you can even select different resolutions per participant, e.g HD for key speakers, and low resolution for others.

- Per participant authentication, permissions, and controls of the resolution, bandwidth, CPU, and other parameters.
Run the entire platform on your premise at no additional cost.

- Native and web based offerings. No additional download required! 

> **Disclaimer**: The Conferencing and Streaming APIs are currently under development for more platforms and will be continuously updated. Please ensure that you are using the latest version of Mesibo APIs and documentation.


### Prerequisites
Before we dive into the various concepts and APIs for conferencing & streaming ensure that you are:

- Familiar with mesibo API. If not, please refer to [Getting Started](https://mesibo.com/documentation/get-started/) guide and tutorials to familiarize yourself
- Familiar with [Group Management and Messaging APIs](https://mesibo.com/documentation/api/backend-api/#group-management-apis). 


# Mesibo Conferencing and Streaming 

Mesibo has made it simple to use and build with group calling and streaming APIs. In just a few steps, you can set up any type of streaming and conferencing application you need- a webinar, virtual meeting and conferencing, live events and more. 

For example, say you want to build a conference room to host a virtual meeting. A conference room is a group, where members are sharing their their video - from their camera (or screen). 

With mesibo, all you need to do to is:
1. Create a group. 
2. Enable the required features for the video stream - such as quality of the video, etc
3. Add participants. People in the group can now see the videos of others in the group and also share their own video
4. That's it. Get busy conferencing!

Quite simple, isn't it? Well the idea behind it is more simple. We are just extending mesibo's group messaging features to include group calling.  In case of group messaging you have a certain set of controls & permissions. With group calling, you just have an additional set of settings along with these permissions. Curious how we do that? Let's dive in.

If you are not familiar with using [Mesibo Group Management APIs](https://mesibo.com/documentation/api/backend-api/#group-management-apis), here's a little recap. Feel free to skip ahead if you are already familiar with group messaging.

### Fundamentals of Group Calling

Mesibo allows you to create groups having a set of users as group members. Once you create a group, you can send messages to the group, and all the group members will receive the messages. To set up a group with Mesibo APIs you follow the steps below:

1. Create a group.
2. Add Members.
3. Grant Permissions about who can send and received messages. For example, you can set permissions such that only members can send, only selected members can send, only Selected Members can receive, etc. You can also grant admin rights to who can change group settings, who can remove members, etc

In group messaging, when you send a message to the common group every member gets a message notification. The user opens the message and reads it. In the same way, in group calls, when you make a call to the group, every member of the group gets a call notification and each member of the group will connect with the call. 

You can restrict which member can send a message, who can only receive a message, etc. By specifying member behavior and permissions you can build [groups of different types](https://mesibo.com/documentation/api/backend-api/#group-management-apis). 

![Group Permissions](group_member_settings.png)

In the case of group messaging, each member has the following permissions:
- can send:  If the member can send messages to the group
- can receive: If the member can receive messages from the group

## Extending group messaging to group calling

Now, for group calling, in addition to above, each member has the following permissions:
- can publish:  If the member can make (publish) group voice or video calls
- can subscribe: If the member can subscribe to group voice or video calls 
- can list: If the member can get a list of active callers in the group

This offers you a very fine level of control that allows you to have to ensure higher levels of privacy and security. It also helps you build exactly what you need your application to be, with maximum customizability. If you can think it, you can build it. Any type of conferencing and streaming app that you need can be easily configured.

### Conference
In a conference room, you need the members of the group to publish their stream and see other's streams. Every member will get a list of participants who are publishing their streams. They can then view the streams of each participant. So, you have the following permissions set.

![conference scenario](conference.png)

### Webinar
In a webinar you only need one(or a few more) members to be publishing. The other participants will only be listening or viewing the streams. Other members will not be allowed to publish their streams. This can be achieved with the following permission levels.
![webinar scenario](webinar.png)

### Core features of conferencing 

The following are the basic features a conferencing application requires.

1. A conference room which people can join
2. Ability to get the list of participants which is updated as and when people join or leave the room in real-time
3. A participant should be able to publish the video or voice to the room
4. A participant should be able to view video published by other participants
5. Mute/Unmute and other controls
6. Authorization so that only rightful people can join the conference

In the following sections, we will learn how to meet the above requirements and how you can use Mesibo APIs to achieve this

## 1. Create the conference application

First, create a new application in [mesibo console](https://mesibo.com/console/). 

Click on the `+ CREATE NEW APPLICATION` button under My Applications section in the console. Enter the name of the application `conference` and click `Create`.

Once your application is created it will be visible in the `My Applications` table. Click on the Settings icon, to display the `App Settings` page for your app. 

In mesibo, the conference room is a group that allows communication between multiple users - that could be a video or a voice conference. Each group contains one or more users who will be participants of this conference call. In a real app, you create users on-demand using mesibo REST APIs (Private backend APIs) to perform the operations. We will explain both the ways of performing these operations, creating a group and adding users - using the console and using REST APIs

## 2. Creating the conference participants
To create users in the console, 

Click on `Users` section in your app.

Click on the `+ NEW USER` button. To create a user, Enter a User Address and App ID. For example, `basic room`  

Similarly,you can create more users.

To create users REST API, follow the steps below:

1. We will ask for the name and email of the user and send an OTP to their email. To do this send a request with the following parameters to send an OTP to the email of the user. `MESIBO_API_BACKEND` is the API url configured in [config.js (https://github.com/mesibo/conferencing/blob/master/live-demo/web/mesibo/config.js)

```
MESIBO_API_BACKEND?op=login&appid=APP_ID&name=NAME&email=USER_EMAIL
```
2. Now we will use a private API to verify this email and generate a token(For more details on mesibo private APIs refer [here](https://mesibo.com/documentation/tutorials/open-source-whatsapp-clone/backend/#user-login-and-authentication)). The user will now need to enter the OTP received which we then send to the backend for verification with the following request
```
MESIBO_API_BACKEND?op=login&appid=APP_ID&name=NAME&email=USER_EMAIL&code=OTP_RECEIVED
```

### 3. Creating the conference group
Go to [mesibo dashboard](https://mesibo.com/console/#/dashboard)and choose the application created earlier(conference) and click on the `Settings` icon. Now click on `Groups` to open the groups page.

To create a new group, click on the `+ NEW GROUP` button. Give a group name - example `ConferenceGroup` and click on `Create`. Your group with the name `ConferenceGroup` should now be created and be displayed in the table. Click on the edit icon, under actions. This will open the Group Settings page.

In a conference room, all the participants will be able to publish live streams and view live streams.

![Conference Group Settings](images/conference_group_settings.png)

In the members table notice that there are no members/participants and the table is empty. We need to add group members to have users in the conference. 

You can set the type of stream you need, such as audio or video or both. For example, you can have an audio-only group call. If you need to show a presentation or some charts, you need to enable screen sharing. You may choose to share multiple streams(simulcast) at once. Both your camera stream and desktop screen can be streamed simultaneously, with your camera feed streaming your self and the screen containing your presentation.

Next, decide what kind of quality you need for your calls. If you need to save some bandwidth or you are on a poor network, you may want to choose VGA. If you can have nothing but the highest quality live stream, select 4K! Mesibo provides you with a wide range of streaming quality options. Note that although you can enable the resolution required here in the console, your camera/device must support recording that resolution. It is recommended that you enable the resolution that best meets your bandwidth and device capacity.

![Group Calling Quality](stream_quality_settings.png)

### Creating the group using REST API

```javascript
const STREAM_RESOLUTION_DEFAULT = 0 ;
const STREAM_RESOLUTION_QVGA  = 1 ;
const STREAM_RESOLUTION_VGA = 2 ; 
const STREAM_RESOLUTION_HD = 3 ;
const STREAM_RESOLUTION_FHD = 4;
const STREAM_RESOLUTION_UHD = 5;
```

You can create a group, by making an API request in the following format:
```
MESIBO_API_BACKEND?token=USER_ACCESS_TOKEN&op=setgroup&name=ROOM_NAME&resolution=ROOM_RESOLUTION
```
For a successful request, you response will look like below:
```
{
    "op": "setgroup",
    "ts": 1592913675,
    "gid": 96734,
    "name": "newroom",
    "type": 0,
    "resolution": "0",
    "publish": 1,
    "pin": "93799667",
    "spin": "35399768",
    "result": "OK"
}
```
You can store all these room parameters.

### Add Members

Now, let us add the users we created earlier as members of this group `ConferenceGroup`. Click on the `+ NEW MEMBER` button and enter the user address, of the user whom you wish to add. In the `User Address` enter `user0` and click on `Add`. The Members table will now display the member you just added. Similarly, add the other four users. 

Refer to [Creating a Group](https://mesibo.com/documentation/api/backend-api/#create-a-group) for more information on creating a group. 

Add the five users created earlier as members of the group.
![Group Members](images/group_members.png)

To enter a room you need to enter a `room-ID` and a `room pin`. In code, you take these parameters, along with the access token(that was generated in the login step) and request mesibo backend to authenticate it by using the following request:
```
MESIBO_API_BACKEND?token= USER_TOKEN &op=joingroup&gid= ROOM_ID &pin= ROOM_PIN
```
### Entering a room == Entering a group
To enter a group you need to enter a `groupid` and a `pin`, along with the access token(that was generated in the login step) and request mesibo backend to authenticate it by using the following request:
```
MESIBO_API_BACKEND?token= USER_TOKEN &op=joingroup&gid= ROOM_ID &pin= ROOM_PIN
```

### Client API
Let's now take a look at the different API functions that you can use to set up your conferencing and streaming platform.
Detailed documentation of each function/method can be found [here]() and will be explained with examples in the next section to build a zoom like conferencing app. 

### Create a group
Use Mesibo's Group Management APIs to create a group. Add members and set permissions for the group members.

### Get the list of members
When any member joins the group, they will be getting a list of other members in the group. The callback function `Mesibo_onParticipant` will be called.

### Place a call to the group
To place a call to the group, first, you need to initialize the group call. For this, you need to use `initGroupCall()` which will provide you with a group call object and then link it with a group using `setRoom`. 

### Connect to voice and video of members
Once you get a list of participants, you can choose to connect to each of those streams. To connect to a participant's stream you need to use the `call` method. 

### Mesibo Streaming and Conferencing SDK
We have released the streaming and conferencing Javascript SDK along with [a sample conferencing app](https://mesibo.com/livedemo) which is a fully functional, Zoom Like Video Conferencing app built using the same. You can also download the entire source code from [github](https://github.com/mesibo/conferencing).



## 2. Getting a list of Participants

Other members, are also mesibo users who are part of the same group(conference room) as you(the publisher). Other group members are also publishing their streams.

Before we get the list of participants, first we need to initialize mesibo and connect to a group.

### Initialize mesibo
To initialize mesibo, create an instance of mesibo API class `mesibo`. Set the app id and token that you obtained while creating the user.
 
You can initialize and run mesibo as follows:
 
```javascript

var api = new mesibo();
api.setAppName(MESIBO_APP_ID);
api.setListener(new mesiboNotify(api));
api.setCredentials(MESIBO_ACCESS_TOKEN);
api.setDatabase("mesibo");
api.start();

```
### Initialize Group Calling & Streaming

To set up group calling and streaming call `initGroupCall()` to create the group call object. 
To link the room with a group, call the `setRoom` method of the group call object, by passing the group-id.

An example in Javascript is as follows,
```javascript
    
//Create group call object
var live = api.initGroupCall();

live.setRoom(DEMO_GROUP_ID);    
```

Now you will get a list of group members through the listener `Mesibo_onParticipants`. You can choose and subscribe to the stream of each member to view it. When a new participant joins the room, `Mesibo_onParticipants` will be called. 

```javascript

mesiboNotify.prototype.Mesibo_OnParticipants = function(all, latest) {    
    for(var i in latest) {
        console.log("Mesibo_Onparticipants: " + latest[i].getId());
        var p = latest[i];
        connectStream(p);
    }
}

```
The parameter `all` contains an array of all participants who have joined.
The parameter `latest` contains the array of participants that have just joined the group.

You can now iterate through the list of participants and subscribe to the stream of each participant.
```javascript
function connectStream(stream){
    for (var i = 0; i < streams.length; i++) {
        if(streams[i] == null){
            streams[i] = stream;
            streams[i].element_id = 'video-remote-'+ i;
            subscribe(streams[i]);
            return;
        }
    }
}
```

### 3. View the streams of participants in the group
You can subscribe to the stream of each participant  that you get in `Mesibo_onParticipants` as follows with the `call()` method
The `call` method takes the following parameters:
- The ID of the HTML element where the video will be rendered
- A callback function `on_stream` where you will be notified of the stream
- A callback function `on_status` where you will be notified when the mute status changes, there is a change in the quality of the stream if the participant has hung up, etc

For example, if the ID of the HTML element where the video will be displayed is `video-stream` then, we can connect to the call as follows:
```javascript
function subscribe(p) {
    console.log('====> subscribe', p.getId(), p.element_id);
    p.call(null, p.element_id, on_stream, on_status);
}


function on_status(p, status, video){
    if(MESIBO_CALLSTATUS_CHANNELUP == status){
        console.log(p.getName()+ 'is connected');
    }
    
    if(MESIBO_CALLSTATUS_COMPLETE == status){
        console.log(p.getName()+ 'has disconnected');
    }
}

function on_stream(p) {
    console.log('on_stream');

    //Local Stream
    if(p.isLocal()) {
        p.attach("video-publisher");
        return;
    }

    //Remote Stream
    console.log('===> on_stream', p.element_id, 'attach');
    p.attach(p.element_id);
}
```

### Publishing your self stream
Call the `getLocalParticipant` method to initialize local publisher(the stream you need to send) 
```javascript

// Create a local participant, Set Publisher name and address
var publisher = live.getLocalParticipant(USER_NAME, USER_ADDRESS); 
publish(publisher);
```    
You are the publisher. As a member of the conference room group, you can stream your self, which other members can view.
If the ID of the HTML Element is `video-publisher` then we publish as follows:

Call `streamFromCamera` when you need to send your camera stream.  
Call `streamFromScreen()` when you need to share your stream

```javascript
function streamFromCamera() {
    console.log('streamFromCamera');

    var o = {};
    o.peer = 0;
    o.name = DEMO_GROUP_NAME;
    o.groupid = DEMO_GROUP_ID;
    o.source = STREAM_CAMERA; 

    console.log('local publisher', o);

    publisher.call(o, "video-publisher", on_stream, on_status);
}

function streamFromScreen() {
        console.log('streamFromScreen');
    
    var o = {};
    o.peer = 0;
    o.name = DEMO_GROUP_NAME;
    o.groupid = DEMO_GROUP_ID;
    o.source = STREAM_SCREEN; 

    console.log('local publisher', o);

    publisher.call(o, "video-publisher", on_stream, on_status);
}

```

### Muting Streams

We can mute video and audio locally, for the streams that we are viewing. You can use the `toggleMute` method to toggle the audio and video status of a stream. 

```javascript
Stream.toggleMute(video, remote)
```
Parameters:
- `video` Boolean , `true` for video, `false` for audio
- `remote` Boolean, `true` for remote stream, `false` for local stream


For example, to mute audio and video of your own stream-- the publisher
```javascript
function toggleSelfVideo() {
    publisher.toggleMute(true, false);
}

function toggleSelfAudio() {
    publisher.toggleMute(false, false);
}


```
To mute remote stream, you use the same method, but the second parmeter is `false`
```javascript
function toggleRemoteVideo(i) {
    var s = streams[i];
    if(s)
        s.toggleMute(true, false);
}

function toggleRemoteAudio(i) {
    var s = streams[i];
    if(s)
        s.toggleMute(false, false);
}

```

### Getting the Mute Status of a stream

You can get the mute status of a stream with `muteStatus` which has the syntax below:
```javascript
Stream.muteStatus(video,remote)
```
Parameters:
- `video` Boolean , `true` for video, `false` for audio
- `remote` Boolean, `true` for remote source, `false` for local source

Example,
```javascript
    remote.muteStatus(true, false); // video mute status-local source
    remote.muteStatus(false, false); // audio mute status-local source
    
    remote.muteStatus(true, true); // video mute status-remote source
    remote.muteStatus(false, true); // audio mute status-remote source
```


