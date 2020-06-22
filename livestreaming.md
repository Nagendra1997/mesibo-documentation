---
description: Enable applications with conference calling, live streaming, screen sharing, group chat and more
keywords: messaging platform, chat api platform, voice, video calling, mesibo communication platform
title: Mesibo Conferencing and Streaming Platform- Conference Calling, Live Streaming, Screen sharing and chat API
---

Mesibo Conferencing and Streaming Platform helps you build applications at scale for teleconferencing, virtual events and webinars, on-demand streaming and more. The platform is available both on the cloud and on-premise at no additional cost.

Mesibo offers a powerful combination of features to build any kind of conferencing and streaming application with minimal setup and cost.

- Just under 700KB footprint makes mesibo, the lightest conferencing server in the world. You can run mesibo's conferencing platform on any server with minimum resources, even on something like a Raspberry Pi.

- Capacity on Demand - Elastic architecture lets you use Kubernetes or Docker Swarm orchestration to scale-up or scale-down capacity on demand!

- Simultaneous publishing to multiple groups - this unique mesibo capability enabled you to make interesting use cases for live-streaming.
Standard, HD, Full-HD, and 4K conference rooms - you can even select different resolution per participant, e.g HD for key speakers and low resolution for others.

- Per participant authentication, permissions, and controls of the resolution, bandwidth, CPU, and other parameters.
Run the entire platform on your own premise at no additional cost.

> **Disclaimer** : The Conferencing and Streaming APIs are currently under development for more platforms and will be continously updated. Please ensure that you are using the latest version of Mesibo APIs and documentation.

# Mesibo Conferencing and Streaming 
Mesibo has made it simple to use and build with group calling and streaming APIs. Mesibo conferencing and streaming can be readily integrated with your existing applications which are using Mesibo messaging and call APIs. In just a few steps, you can setup any type of streaming and confrencing application you need- a webinar, virtual meeting and conferencing, live events, etc. 

For example, say you want to build a conference room to host a virtual meeting. A conference room is basically a group, where members are streaming their camera feed. So to create a group conference call, you need to create a group. You can [create a group](https://mesibo.com/documentation/api/backend-api/#group-management-apis) from the console or use the backend APIs. Then, you can enable the required features for the calls. 

### Call Type
You can set the type of stream you need, such as audio or video or both. For example, you can have an audio only group call. If you need to show a presentation or some charts, you need to enable screen sharing. You may choose to share multiple streams(simulcast) at once. Both your camera stream and desktop screen can be streamed simulataneously, with your camera feed streaming your own self and the screen containing your presentation.

### Call Quality 
What kind of quality do you need for your calls? If you need to save some bandwidth or you are on a poor network, you may want to choose VGA. If you can have nothing but highest quality live stream, turn on 4K! Mesibo provides you with a wide range of streaming quality options. Note that although you can enable the resolution required here in the console, your camera/device must support recording that resolution. It is recommended that you enable the resolution that best meets your bandwidth and device capacity.

![Group Calling Quality](stream_quality_settings.png)

That's it. You are now good to go! You can now use Mesibo's group calling APIs and build a conferencing app.

This sounds good enough for a basic conference call. But, a one size fits all approach will not work for all your needs. For example, you may need a conference room where only the department heads are allowed to be publishing their stream, while all others are by default muted.There can also be admins or moderators who have special permissions to change group settings, remove participants, mute members, etc. How can you build something like this?  

You can achieve this with a fine control on who can connect and publish to your room, what kind of streams they can send or receive. In fact with Mesibo's custom group settings and permissions you can create any kind of streaming and conferencing app you require. 

Mesibo offers you these settings at two levels: 
1. Group Level: You can apply these settings at the group level, which will apply to all the members of the group
2. Member Level: You can modify settings at the individual member level

In case of group messaging you have the same type of controls. With group calling you just have an additional set of settings along with these permissons. Mesibo Group Calling APIs simply extend the features offered in group messaging.

### Group Calling - That's just Group messaging with extra steps!
In your console, along with the settings for a normal group we will now see new settings for group calling. Mesibo APIs for conferencing, extend the core features offered by mesibo [Group Management and Messaging APIs](https://mesibo.com/documentation/api/backend-api/#group-management-apis). 

If you are not familiar with using [Mesibo Group Management APIs](https://mesibo.com/documentation/api/backend-api/#group-management-apis), here's a little recap. Feel free to skip ahead if you are already familiar with group messaging.

## Group Messaging
Mesibo allows you to create groups having a set of users as group members. Once you create a group, you can send messages to the group, and all the group members will receive the messages. To set up a group with Mesibo APIs you follow the steps below:

1. Create a group.
2. Add Members.
3. Grant Permissions about who can send and received messages. For example you can set permissions such that only members can send, only selected members can send, only Selected Members can receive, etc. You can also grant admin rights to who can change group settings, who can remove members, etc

In group messaging, when you send a message to the common group every member gets a message notification. The user opens the message and reads it. In the same way, in group calling, when you make a call to the group, every member of the group get's a call notification and each member of the group will connect with the call. 

You can restrict which member can send a message,who can only recieve message, etc. By specifying member behaviour and permissions you can build [groups of different types](https://mesibo.com/documentation/api/backend-api/#group-management-apis). 

![Group Permissions](group_member_settings.png)

In case of group messaging, each member has the following permissions:
- can send :  If member can send messages to the group
- can receive: If member can receive messages from the group

Now, for group calling, in addition to above, each member has the following permissions:
- can publish :  If member can make (publish) group voice or video calls
- can subscribe: If member can subscribe to group voice or video calls 
- can list: If member can get a list of active callers in the group

This offers you a very fine level of control that allows you to have to ensure higher levels of privacy and security. It also helps you build exactly what you need your application to be, with maximum customizability. If you can think it, you can build it. Any type of conferencing and streaming app that you need can be easily configured.

### Conference
In a conference room you need the members of the group to publish their own stream and see other's streams. Every member will get a list of participants who are publishing their streams. They can then view the streams of each participant. So, you have the following permissions set.

![conference scenario](conference.png)

### Webinar
In a webinar you only need one(or a few more) members to be publishing. The other participants will only be listening or viewing the streams. Other members will not be allowed to publish their own streams. This can be achieved with the following permission levels.
![webinar scenario](webinar.png)

This is for an open webinar. So, what would you do in case of a members only webinar? You can change the permission as follows
Who can view live streams: Members , Who can view streams list: Members 

### Class Room
If you have an online classroom, the teacher has the controls to change the permissions of the students. At the group level, you can set that only selected members can publish- The teachers.

![class room](classroom-1.png)

The students by default do not have the permission to publish. By default you can mute all the students. The teacher can dynamically set which student can talk when any clarification is needed. You can have this dynamic control, by individual member level permissions.

![student in class room](classroom-2.png)

You can similarly build the set of required permissions for any scenario.In case of a video streaming app, You can upload media to the group, which only selected members can view on-demand, whenever they need it. All of this can be built by setting the different group calling permissions.

Let's now take a look at the different API functions that you can use to set up your conferencing and streaming platorm.
A detailed documentaion of each function/method can be found [here]() and will be explained with examples in the next section to build a zoom like conferencing app. 

### Create a group
Use Mesibo's Group Management APIs to create a group. Add members and set permissions for the group members.

### Get list of members
When any member joins the group, they will be getting a list of other members in the group. The callback function `Mesibo_onParticipant` will be called.

### Place a call to group
To place a call to the group, first you need initialize the group call. For this you need to use `initGroupCall()` which will provide you with a group call object and then link it with a group using `setRoom`. 

### Connect to voice and video of members
Once you get a list of participants, you can choose to connect to each of those streams. To connect to a participant's stream you need to use the `call` method. 

# Mesibo Live Demo App

In this section we will build Mesibo Live- a video conferencing app like Zoom. Before you proceed, please make sure that you have read the prerequisites and are familiar with mesibo APIs. First we will build a simple, basic version of the group calling app using basic HTML/CSS and Javascript . Then we will build the complete app with all the necessary features.

You can download the source code for the basic group calling demo [here](https://github.com/mesibo) to understand the basic functionality of conferencing and steaming APIs

Checkout [Mesibo Live Demo](https://mesibo.com/livedemo) which is a fully functional, Zoom Like Video Conferencing app. You can also download the entire source code from [github](https://github.com/mesibo). 


## mesibo Basic Conferencing  
This repository contains the source code for the basic conferencing demo.

The basic conferencing demo will help you get familiar with using mesibo conferencing APIs. For this demo, we will build a conference room up to 5 Participants. Each participant will be able to publish their video, get a list of participants in the conference, and view (subscribe to) other participant's videos. Each participant will able to share their camera feed or share their screen, which will be visible to all the participants. Please note that it is recommended that you use the demo app on a desktop for a better experience.

Once you are familiar with the basic conferencing functionality and the API functions, you can go on to build a full-featured conferencing app with unlimited participants, group messaging, personal chat, etc. - [mesibo Live](https://github.com/mesibo/conferencing/livedemo). You can also try it here [https://mesibo.com/livedemo](https://mesibo.com/livedemo)

### Prerequisites
- This demo uses mesibo Javascript SDK. Ensure that you are familiar with using it.
- Familiarity with mesibo API. Refer [Getting Started Guide](https://mesibo.com/documentation/get-started/) and [First App Tutorial](https://mesibo.com/documentation/tutorials/first-app/#preparation) if you are not familiar.
- Familiarity with mesibo [User and Group Management APIs](https://mesibo.com/documentation/api/backend-api/#group-management-apis)
- A basic understanding of HTML/CSS/JS

### Core features needed for conferencing 

The following are the basic features a conferencing app requires.

1. A conference room which people can join
2. Ability to get the list of participants which is updated as and when people join or leave the room in real-time
3. A participant should be able to publish the video or voice to the room
4. A participant should be able to view video published by other participants
5. Mute/Unmute and other controls
6. Authorization so that only rightful people can join the conference

In the following sections, we will learn how to meet the above requirements.

### Get Source Code

Download the [Source Code](https://github.com/mesibo/conferencing). A recommended way is to clone the repo.

```
$ git clone https://github.com/mesibo/conferencing
```

### UI Elements needed for group calling
This demo will have basic UI elements containing the local video stream, the remote video streams, buttons for muting audio/video streams, a button to share our screen, and exit the group or hangup.

For this demo, we will just have one local video stream and four remote video streams. You can modify accordingly for more users/streams. Refer `room.html`

## 1. Creating a Conference Room

First, create a new application in [mesibo console](https://mesibo.com/console/). 

Click on the `+ CREATE NEW APPLICATION` button under My Applications section in the console. Enter the name of the application `conference` and click `Create`.

In mesibo, the conference room is a group that allows communication between multiple users - that could a video or a voice conference and group chat. Each group contains one or more users who will be participants of this conference call. In a real app, you create users on-demand using mesibo API. However, for this demo, we will create a few users using the mesibo console to quickly understand the concepts.

### Create Users

Once your application is created it will be visible in the `My Applications` table. Click on the Settings icon, to display the `App Settings` page for your app. Click on `Users`. We have five participants needed in our conference, so we need to create five users.

Click on the `+ NEW USER` button. To create a user, Enter a User Address and App ID. 

To create our first user, enter the address as `user0` and the app id as `basicroom`. Then click `Create`. The `user0` should now be displayed in the table. Set `MESIBO_APP_ID` as `basic room` in `demo.js`.  

```javascript
const MESIBO_APP_ID = 'basicroom';
```

Similarly, create four more users with the same App ID `basicroom`. 

You now have five users
![Conference Users](images/users_list.png)

Now for a user to login to mesibo, the user needs a token. To get a token for a user, click on the `Edit` icon. Now in the `Edit User` page, copy the `Auth Token`. Replace the `TOKEN_USER_0` with the token you just copied. Replace `ADDRESS_USER_0` with `user0`.

![User Token](images/user_token_0.png)

Similarly get the token for other users and configure `demo_users` in the script `demo.js` with the respective token and user address.

```javascript
var demo users = [
    {
     'token' : 'TOKEN_USER_0'
     ,'address'  : 'ADDRESS_USER_0'
     ,'name'    : 'User-0' 
    },

    {
     'token' : 'TOKEN_USER_1'
     ,'address'  : 'ADDRESS_USER_1'
     ,'name'    : 'User-1' 
    },

    {
     'token' : 'TOKEN_USER_2'
     ,'address'  : 'ADDRESS_USER_2'
     ,'name'    : 'User-2' 
    },

    {
     'token' : 'TOKEN_USER_3'
     ,'address'  : 'ADDRESS_USER_3'
     ,'name'    : 'User-3' 
    },

    {
     'token' : 'TOKEN_USER_4'
     ,'address'  : 'ADDRESS_USER_4'
     ,'name'    : 'User-4' 
    },

] 
```
For example, this is how your configuration should look like. 

```javascript
var demo_users = [
    {
     'token' : '6e4a59ab53b82c8edd149ac591a6613'
     ,'address'  : 'user0'
     ,'name'    : 'User-0' 
    },

    {
     'token' : '2d7a9876e9a20847f18cf32dfbea13612f'
     ,'address'  : 'user1'
     ,'name'    : 'User-1' 
    },

    {
     'token' : 'a2a403cd731fd305cb75fe4a00a9e11135d90'
     ,'address'  : 'user2'
     ,'name'    : 'User-2' 
    },

    {
     'token' : 'c5701f652721b8388e0e148a8d722136130'
     ,'address'  : 'user3'
     ,'name'    : 'User-3' 
    },

    {
     'token' : 'a1f65cb75fe4a5288ebf97ab8f18a8d7e22136130'
     ,'address'  : 'user4'
     ,'name'    : 'User-4' 
    },

] 
```

Once you are finished configuring the users, we can now move to create a group and add the users we create as members of the group.

### Create a Group
Go to [mesibo dashboard](https://mesibo.com/console/#/dashboard)and choose the application created earlier(conference) and click on the `Settings` icon. Now click on `Groups` to open the groups page.

To create a new group, click on the `+ NEW GROUP` button. Give a group name - example `ConferenceGroup` and click on `Create`. Your group with the name `ConferenceGroup` should now be created and be displayed in the table. Click on the edit icon, under actions. This will open the Group Settings page.

Now, in the script `demo.js` you need to configure the groupid and group name for the group you just created. Set `DEMO_GROUP_ID` and `DEMO_GROUP_NAME`.

For example, if your groupid is `96191` and group name is `mesibo`
```javascript
const DEMO_GROUP_ID = 96191;
const DEMO_GROUP_NAME = "ConferenceGroup";
```

For the simplicity of our app, we will keep all the settings at default. In our conference room, all the participants will be able to publish live streams and view live streams.

![Conference Group Settings](images/conference_group_settings.png)

In the members table notice that there are no members/participants and the table is empty. We need to add group members to have users in the conference. 

### Add Members

Now, let us add the users we created earlier as members of this group `ConferenceGroup`. Click on the `+ NEW MEMBER` button and enter the user address, of the user whom you wish to add. In the `User Address` enter `user0` and click on `Add`. The Members table will now display the member you just added. Similarly, add the other four users. 

Refer to [Creating a Group](https://mesibo.com/documentation/api/backend-api/#create-a-group) for more information on creating a group. 

Add the five users created earlier as members of the group.
![Group Members](images/group_members.png)

### Logging-In with the selected user
Before you proceed ensure that you have made the necessary configuration for your group and users.

Open the `room.html` page. There is a button for each user. Click on the user button that you want to login with, which when clicked will select the user and login to mesibo with the token of the selected user.
```javascript
function login(user_index){
    var selected_user = demo_users[user_index];
    
    //Initialize mesibo Group Calling

```

Load the `index.html`  Click on the user button to log in. 

To test the demo, 
1. On one browser window, launch `room.html`. The connection status button will show `Offline`. Click on `User-0`. The connection status button will now show `User-0 is online`. You have now logged in as `User-0`. Your camera feed will automatically connect. Grant required permission for audio and video.
2. Open another browser window and launch `room.html`. Click on `User-1`. The connection status button will now show `User-1 is online`. You have now logged in as `User-1`. Click on the `Share-Screen` button and your screen sharing feed will start. Grant required permissions.
3. You should now see the camera feed of `User-0` being received at `User-1`. The screen feed being sent from `User-1` should be seen at `User-0`. 
4. If you can send and view videos at `User-0` and `User-1`, then you can continue to connect with more users. Similarly, you can open the same web page, `room.html` on different browser windows or devices, and log in as different users, to connect your feed to the group. If you need to test with more users, add more users to the `demo_users` list and modify the UI accordingly. 
5. When you connect with more users, sharing their streams, the grid should look like below:

In the following sections, we will understand how all of this works and how you can use mesibo APIs for conferencing and streaming.


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

We can mute video and audio locally, for the streams that we are viewing. You can use `toggleMute` method to toggle the audio and video status of a stream. 

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


# Building a Zoom Like Conferencing app 

Video conferencing apps like Zoom and Google Meet are showing new ways for people to virtually collaborate and connect. Streaming services like Netflix, Youtube, Prime Video, etc have forever changed the way people consume entertainment and media.

Let us now build a Zoom like Conferencing app using Mesibo Conferencing and Streaming APIs.

You can try the [Mesibo Live Demo(Beta)](https://mesibo.com/livedemo) which is a fully functional, Zoom Like Video Conferencing app and also download the entire source code from [Github](https://github.com/mesibo/conferencing). 

![Mesibo Room](mesibo-room.png)

### Prerequisites

- We will be using the Mesibo Javascript SDK. So, install Mesibo Javscript SDK by following the instructions [here](https://mesibo.com/documentation/install/javascript/)
- Familiar with Mesibo [User and Group Management APIs](https://mesibo.com/documentation/api/backend-api/#group-management-apis)
- Familiar with the basic concepts of how Mesibo APIs for streaming and conferencing work
- A basic understanding of HTML/CSS/JS
- A minimal understanding of Bootstrap
- Not an absolute need, but a familiarity with Angular would be good to have

Let's get started!

### Basic Requirements

We need the following features.
1. A conference room which people can join
2. A list of participants and a way to update the list of participants as and when people join or leave the room
3. View the videos of participants in the group
4. Send my own video, to the group.
5. Mute audio/video of other participants and my own

## 1. Creating a Conference Room

The conference room is a group. We will use REST APIs to perform the operations to create a group and join a group on Mesibo backend. Only the members of a group, will be able to view the streams of other members of the same group.

### Creating a User
Before creating a group, we need to create a mesibo user for the admin. We will be using the token that we receive in this step - the access token of the admin user, while creating the group in the next step. Note that anyone who wants to join the group, also need to be a mesibo user with a token. 

So, for the first step we need to create a login form, where we authenticate them and generate a token for them.

![login](login.png)

1. We will ask for the name and email of the user and send an OTP to their email. To do this send a request with the following parameters to send an OTP to the email of the user.
```
https://app.mesibo.com/conf/api.php?op=login&appid=APP_ID&name=NAME&email=USER_EMAIL
```
2. The user will now need to enter the OTP receieved which we then send to backend for verification with the following request
```
https://app.mesibo.com/conf/api.php?op=login&appid=APP_ID&name=NAME&email=USER_EMAIL&code=OTP_RECEIVED
```
If the entered OTP matches, we generate a token for that user, you will receive a token in the response. Save the token. You can refer to the `getMesiboDemoAppToken()` function in `login.js`.


### Creating a Room
For a conference room we need to create a group that other people can join. The creator of the room, will configure all the room properties.

For better safety and privacy, we can also set a pin or password to our group. When anyone needs to enter the group they need to enter this pin. This is optional. If you do not need this, do not use the pin parmeter while sending the request.

For simplicity, we will only set the room name and pin for now. We will be creating a normal group where all members can send and receive streams.

![create room](create-room.png)

You can choose between two room types: Webinar and conference. In case of webinar, participants should not be allowed to publish by default.
```javascript
const GROUP_TYPE_CONFERENCE = 0;
const GROUP_TYPE_WEBINAR = 1;
```

We can also set the video quality settings required.
```javascript
const STREAM_RESOLUTION_DEFAULT = 0 ;
const STREAM_RESOLUTION_QVGA  = 1 ;
const STREAM_RESOLUTION_VGA = 2 ; 
const STREAM_RESOLUTION_HD = 3 ;
const STREAM_RESOLUTION_FHD = 4;
```

If you are hosting [Mesibo Backend](https://github.com/mesibo/messenger-app-backend), modify the REST Endpoint accordingly.
Here, we will use `https://app.mesibo.com/conf/api.php`.

You can create a group, by making a request in the following format:
```
https://app.mesibo.com/conf/api.php?token=USER_ACCESS_TOKEN&op=setgroup&name=ROOM_NAME&pin=ROOM_PIN&type=ROOM_TYPE&resolution=ROOM_RESOLUTION
```
## 2. Getting a list of Participants

Other members, are also mesibo users who are part of the same group(conference room) as you(the publisher). Other group members are also publishing their own streams.

Before we get the list of participants, first we need to initialize mesibo and connect to a group.

### Initialize Mesibo
To initialize Mesibo, create an instance of Mesibo API class `Mesibo`. Set the app id and token that you obtained while creating theuser.
Call the `getLocalParticipant` method to initialize local publisher(the stream you need to send) 
You are the publisher. As a member of the conference room group you can stream your own self, which other members can view.

You can initialize and run mesibo as follows:
 
```javascript

	$scope.initMesibo = function(){
		$scope.mesibo = new Mesibo();

		//Initialize Mesibo

		MesiboLog(MESIBO_APP_ID, $scope.user.token, 'initMesibo');
		$scope.mesibo.setAppName(MESIBO_APP_ID);
		if( false == $scope.mesibo.setCredentials($scope.user.token))
			return -1;

		$scope.mesibo.setListener($scope);
		$scope.mesibo.setDatabase("mesibo");
		$scope.mesibo.start();

		$scope.live = $scope.mesibo.initGroupCall();        
		$scope.live.setRoom($scope.room.gid);
		$scope.publisher = $scope.live.getLocalParticipant($scope.user.name, $scope.user.address);
		if(!isValid($scope.publisher))
			return -1;        


		MesiboLog('publisher', $scope.publisher);

		$scope.call = new MesiboCall($scope);
		$scope.file = new MesiboFile($scope);

		$scope.refresh();

		return 0;
	}


```
### List of Participants

Now you will get a list of group members through the callback function `Mesibo_onParticipants`. You can choose and subscribe to the stream of each member to view it. When a new participant joins the room, `Mesibo_onParticipants` will be called. 

```javascript
	$scope.Mesibo_OnParticipants = function(all, latest) {

		MesiboLog('Mesibo_OnParticipants', all, latest);
		for(var i in latest) {
			var p = latest[i];
			if(isValid(p.getAddress) && isValid(p.getName()))
				$scope.addressBook[p.getAddress()] = p.getName();
			$scope.subscribe(p);			
			playSound('assets/audio/join');
			$scope.addTicker(p.getName() + ' has entered the room');
		}
	}


```

You can now iterate through the list of participants and subscribe to the stream of each participant.

### 3. Subcribe to the streams of participants in the group
You can subscribe to the stream of each participant  that you get in `Mesibo_onParticipants` as follows with the `call()` method. We need to update two lists `$scope.participants` & `$scope.streams`. 

```javascript
	$scope.subscribe = function(p) {
		MesiboLog('subscribe', p);

		p.isVisible = true;
		p.isSelected = false;
		p.isFullScreen = false;
		p.isConnected = true;

		$scope.updateParticipants(p);
		$scope.updateStreams(p);	
	}
	
	$scope.updateParticipants = function(p){
		MesiboLog('updateParticipants', p, $scope.participants, $scope.streams);
		if(!isValid(p))
			return;

		for(var i = 0; i < $scope.participants.length; i++){ 
			if ( $scope.participants[i].getId() === p.getId()) { 
				MesiboLog('updateParticipants','existing');
				$scope.participants[i] = p;
				return;
			}
		}

		$scope.participants.push(p);
	}

	$scope.updateStreams = function(p){
		MesiboLog('updateStreams', p, $scope.participants, $scope.streams);
		if(!isValid(p))
			return;

		for(var i = 0; i < $scope.streams.length; i++){ 
			if ( $scope.streams[i].getId() === p.getId()) { 
				MesiboLog('updateStreams','existing');
				$scope.streams[i] = p;
				return;
			}
		}

		$scope.streams.push(p);		

		$scope.setGrid($scope.streams.length);

		$scope.$applyAsync(function()  {
			MesiboLog('call stream', p);
			p.call(null, "video-"+ p.getId(), $scope.on_stream, $scope.on_status);
		});

	}	
```

### 4. Displaying the grid of videos

We need to dynamically render the grid of videos from the list of streams. That is, if there is only a single video we need to display the video upto the full width of the screen. But, if there are four streams , we need to split the available screen into four equal parts. If there are more, our grid will be divided into more pieces. (As of now we will have a maximum of 16 streams to be displayed at a single time on the screeen)

To build this feature we will use Bootstrap [Column Wrapping](https://getbootstrap.com/docs/4.0/layout/grid/#column-wrapping).

Based on the `grid_mode` we will define the number of columns our grid will have. Based on the number of streams, we will define the grid mode.

```javascript

	$scope.setGrid = function(stream_count){
		MesiboLog('==> setGrid', 'stream_count', stream_count, 'grid_mode', $scope.grid_mode);
		var isGridChange = false;
		var previous_grid_mode = $scope.grid_mode;

		if(!isValid(stream_count) || stream_count <= 0){
			$scope.grid_mode = DEFAULT_GRID_MODE;
			return isGridChange;
		}
		

		if(1 == stream_count)
			$scope.grid_mode = 1;
		else if(stream_count >=2 && stream_count <=4 )
			$scope.grid_mode = 2;
		else if(stream_count >=5 && stream_count <=9 )
			$scope.grid_mode = 3;
		else if(stream_count >=10 && stream_count <=16 )
			$scope.grid_mode = 4;
		else
			$scope.grid_mode = 4; /** Maximum 16 thumbnails can be displayed for now **/

		MesiboLog('==> setGrid', 'stream_count', stream_count, 'grid_mode', $scope.grid_mode);

		if(previous_grid_mode != $scope.isGridChange)
			isGridChange = true;

		$scope.refresh();

		return isGridChange;
	}


```
And our grid rendering is as follows
```html
<div ng-repeat="s in streams track by $index" ng-if="streams[$index] != null"  class ="pl-0 pr-0" ng-class="{'col-md-12': grid_mode==1, 'col-md-6': grid_mode==2, 'col-md-4': grid_mode==3, 'col-md-3': grid_mode==4>
```
