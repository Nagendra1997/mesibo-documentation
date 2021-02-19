---
description: Enable applications with conference calling, live streaming, screen sharing, group chat and more
keywords: conferencing, group calling, live streaming, facebook live, youtube live, screen sharing
title: Mesibo Conferencing and Streaming API Documentation
---

Mesibo Conferencing and Streaming Platform helps you build applications at scale for teleconferencing, virtual events, webinars, on-demand streaming, and more which can be deployed both on cloud and on-premise, at no additional cost.

Mesibo offers a powerful combination of features to build any kind of conferencing and streaming applications with minimal setup and cost.

## Why mesibo for conferencing?
- **Standard, HD, Full-HD, and 4K** conference rooms - you can even select different resolutions per participant, e.g HD for key speakers, and low resolution for others

- **Multiple screen sharing**, not just the one. Allows your participants to share multiple screens if required

- **Simultaneous publishing** to multiple groups - this unique mesibo capability enables you to make interesting use cases for live-streaming.

- Per participant authentication, permissions, and controls over resolution, bandwidth, CPU, and other parameters.

- Run the entire platform on your premise at no additional cost. An elastic architecture lets you run multiple instances to scale-up or scale-down capacity on demand. Mesibo offers you options to scale manually or use Kubernetes or Docker Swarm orchestration.

- Just under 700KB footprint makes mesibo, the lightest conferencing server in the world. You can run mesibo's conferencing platform on any server with minimum resources.

- Native and web-based offerings. No additional download required! 

> **Important**: The Conferencing and Streaming APIs are currently under continuous development and hence will be regularly updated. 

## Prerequisites
Before we dive into the various concepts and APIs for conferencing & streaming, please ensure that you have gone through the following and you are quite familiar with mesibo APIs. 

- [Get Started Guide]({{ '/documentation/tutorials/get-started/' | relative_url }}).

- Tutorial on [Writing your First mesibo Enabled Application]({{ '/documentation/tutorials/first-app/' | relative_url }}).

- Familiar with [Group Management and Messaging APIs](https://mesibo.com/documentation/api/backend-api/#group-management-apis). 

## Terminology

In this document, we will be using the following terms

- Conference - Group Call, Webinar, live streaming, etc. For this document, we will refer to all the use cases as Conference. 
- Stream - video (camera, screen, etc) and voice data
- Participant - a user participating in the conference
- Publishing - publishing/sending self video and voice stream to the conference
- Subscribing - Receiving video and voice stream of other participants
- Publisher - The participant sending video and voice streams
- Subscriber - The participant viewing other's voice and video streams

## Mesibo Conferencing and Streaming 

mesibo has made it simple to build conferencing with the group calling and streaming APIs. In just a few steps, you can set up any type of streaming and conferencing application you need- webinar, virtual meeting and conferencing, live events, and more. 

You can set up any type of conferencing application in just a few steps using group calling and streaming APIs. mesibo group calling simply extends the existing features of group messaging with additional permissions and control, for example, room resolution, talk detection, who can publish and who can video, voice+video or voice only, etc. Your existing groups will now have additional conferencing settings.

### Fundamentals of Group Messaging & Group calling

mesibo allows you to create groups having a set of users as group members. Once you create a group, you can send messages to the group, and all the group members will receive the messages. You only need to do the following to set up a group with mesibo APIs:

1. Create a group with required permissions and settings
2. Add Members (participants)
3. Grant Permissions about who can send and receive messages. 

That's it. Now members can start sending and receiving messages in the group.

Group calling is no different. You just have additional permissions for participants who can publish and who can subscribe.

![Group Permissions](/documentation/conferencing/images/group_member_settings.png)

In the case of group messaging, each member has the following permissions:
- **can send**:  If the member can send messages to the group
- **can receive**: If the member can receive messages from the group

Now, for group calling, in addition to the group settings above, each member has the following permissions:
- **can publish**:  If the member can make (publish) group voice or video calls
- **can subscribe**: If the member can subscribe to group voice or video calls 
- **can list**: If the member can get a list of active callers in the group

This allows you to create different types of conferencing and streaming app. You can configure default group-level permissions as well as per participant level permissions. The following are some examples of how permissions can be set to different types of conferences.

### Conference / Group Calling
In a conference/group calling app, you require that all the members of the group publish their stream and subscribe to other streams. Also, all the members should be able to get a list of participants who are publishing their streams so that they can subscribe to them. Hence the suggested group setting is as shown below. We have selected `Members` only option for who can publish streams, who can view streams, who can view the list of participants.

![conference scenario](/documentation/conferencing/images/conference.png)

### Open Webinar / Panel discussion
In an open webinar or panel discussion app, you generally require that only one or selected participants are publishing and rest are subscribers only. Other members will not be allowed to publish their streams. This can be achieved with the following group level permission levels
 
![webinar scenario](/documentation/conferencing/images/webinar.png)

In case of a members-only webinar, you can simply change the permission as follows

 - Who can view live streams: Members 
 - Who can view stream list: Members 
 
You can also dynamically enable audio or video. For example, in case of a webinar-discussion, someone can 'raise hand' to ask a question and you can automatically enable voice for that participant.

### Class Room
In a classroom app, the teacher has the controls to change the permissions of the students. At the group level, you can set that only selected members can publish - usually the teachers.

![Classroom](/documentation/conferencing/images/classroom-1.png)

The students by default do not have the permission to publish. By default, you can mute all the students. The teacher can dynamically set which student can talk when any clarification is needed. You can have this dynamic control, by individual member level permissions.

![student in class room](/documentation/conferencing/images/classroom-2.png)

You can create any kind of scenario just by setting the right permission. 

### Conference Call Settings

In addition to permissions, you can set various properties like resolution, media, bitrate control, etc. for each group. 

You can decide the default resolution for the conference room. mesibo supports different conference room resolutions, from normal to 4K. Mesibo has an adaptive algorithm to change the bitrate and resolution depending on the network condition, which is ON by default. However, you can disable it if you like.  Note that although you can select the resolution in the console, your camera/device must support that resolution. It is recommended that you enable the resolution that best meets your bandwidth and device capacity.

You can enable default media types (voice or video or both) allowed in your conference room. For example, a normal conference room will have both voice and video. However, you can also create a voice-only room by disabling video. Alternatively, you can only allow subscribers to access for video and voice but only allow asking questions using voice. 

Similarly, you can enable screen-sharing for participants to share one or more screens during the conference. 

Mesibo also allows you to enable simulcast in which each publisher can publish the same stream in different resolutions to cater to subscribers having different bandwidth and devices. Note that enabling simulcast increases CPU and bandwidth usage significantly. 

![Group Calling Quality](/documentation/conferencing/images/stream_quality_settings.png)

You can override these settings per participant using the mesibo REST API. For example, in your conference, you can make a setting such that only selected members (key speakers) can publish in HD resolution and others in low resolution, and so on.  

We will learn more about this in the further sections.

## How to create a conferencing app using mesibo API 

The following are the basic requirements of a conferencing app.

1. A conference room which people can join
2. Ability to get the list of participants which is updated as and when people join or leave the room in real-time
3. A participant should be able to publish the video or voice to the room
4. A participant should be able to view video published by other participants
5. Mute/Unmute and other controls
6. Group and one-to-one chat
7. Authorization so that only rightful people can join the conference


In the following sections, we will learn how to use mesibo APIs to achieve this. We will be using two types of mesibo APIs:

1. mesibo backend APIs for administrative tasks such as creating groups, adding participants, etc.
2. mesibo Real-time APIs for real-time conferencing and streaming. These APIs are on the users’ side.

Let's get started.

### 1. Create the conference application

First, create a new application in [mesibo console](https://mesibo.com/console/). You can skip this step if you already have an existing app.

Click on the `+ CREATE NEW APPLICATION` button under My Applications section in the console. Enter the name of the application `conference` and click `Create`.

Once your application is created it will be visible in the `My Applications` table. Click on the Settings icon, to display the `App Settings` page for your app. 

### 2. Create the conference participants (users)

In mesibo, the conference room is a group that allows communication between multiple users - that could be a video or a voice conference. Each group contains one or more users who will be participants of this conference call. To create participants, you need to create mesibo users. mesibo will create an access token for each user and give it to you which you can send it to your users. Your user can then use this access token in mesibo Real-time APIs using `setAccessToken` function to connect to mesibo. 

Read [Get Started Guide]({{ '/documentation/tutorials/get-started/' | relative_url }}) and [Writing your First mesibo Enabled Application]({{ '/documentation/tutorials/first-app/' | relative_url }}) for a better understanding of mesibo users and tokens.

In a real app, you create groups and participants on-demand using mesibo REST APIs. We will explain both the ways of performing these operations, creating a group, and adding users - using the console and using REST APIs.

To create users in the console, 

Click on the `Users` section in your app.

Click on the `+ NEW USER` button. To create a user, Enter a User Address and App ID. For example, `conf.mesibo.com`  

Similarly, you can create more users in the console.

If you want to add a user with REST API, you need to invoke [useradd](https://mesibo.com/documentation/api/backend-api/#add-a-user--regenerate-a-user-access-token)

Now we need to create a group and make the users that we have created, members of that group.

### 3. Create a group to hold the conference
Go to [mesibo dashboard](https://mesibo.com/console/#/dashboard) and choose the application created earlier(conference) and click on the `Settings` icon. Now click on `Groups` to open the groups page.

To create a new group, click on the `+ NEW GROUP` button. Give a group name - example `ConferenceGroup` and click on `Create`. Your group with the name `ConferenceGroup` should now be created and be displayed in the table. Click on the edit icon, under actions. This will open the Group Settings page. Replicate settings shown below.

![Conference Group Settings](/documentation/conferencing/images/conference_group_settings.png)

If you want to create a group using REST, you need to invoke [groupadd](https://mesibo.com/documentation/api/backend-api/#create-a-group):

For example,
```
https://api.mesibo.com/api.php?op=groupadd&token=xxxxxxxxxxxxx&name=ConferenceGroup&flag=0
```

This will return the group ID in a JSON response. Make note of the group id. We will use this later to add members to the group

```
{"group":{"gid":12345},"op":"groupadd","result":true}
```

### 4. Add Members

Now, let us add the users we created earlier as members of this group `ConferenceGroup`. Click on the `+ NEW MEMBER` button and enter the user address whom you wish to add. Click on `Add`. The Members table will now display the member you just added. Similarly, add more users. 

Add the users created earlier as members of the group in the console.
![Group Members](/documentation/conferencing/images/group_members.png)

If you want to add members to a group using REST API, you need to invoke [groupeditmembers](https://mesibo.com/documentation/api/backend-api/#add-or-remove-group-members) and pass the GID obtained of the group you created in the previous step.

For example,
In the case of a conference, members can send and receive videos. So we invoke the API as follows.
Let's say you have users with addresses `user0`, `user1`, `user2`, `user3`, `user4` that need to be members of the conference, let all members have permission to publish, subscribe, and list. Invoke the API as follows

```
https://api.mesibo.com/api.php?op=groupeditmembers&token=xxxxxxxxxxxxx&gid=12345&m=user0,user1,user2,user3,user4&canpub=1&cansub=1&canlist=1&delete=0
```

If you want to deny certain permission to the user, set the permission to `0`. For example, if you do not want a certain participant to publish, set `canpub=0`.
 
## Client API

Once you have created a group and added your users as members, your users can start conferencing using client-side API. Below is a description of each API. You can refer to our open-source [conferencing app](https://github.com/conferencing) source code available on GitHub for better understanding. It is assumed that you are familiar with mesibo real-time APIs.

Here is a brief overview of the different API methods and callbacks we will be using.

### Initialize mesibo
First, initialize mesibo by creating an instance of mesibo API class `Mesibo`. Set the app id and token that you obtained while creating the user.
 
```javascript

//Create Mesibo API instance
var api = new Mesibo();

//Set Application ID
api.setAppName(MESIBO_APP_ID);

//Create Mesibo Listener object and set it 
api.setListener(new mesiboNotify(api));

//Set the user access token
api.setCredentials(MESIBO_ACCESS_TOKEN);

//Set the database name to store messages,etc
api.setDatabase("mesibo");

//Run Mesibo
api.start();

```
### Initialize the group call 

To set up a group call, use [MesiboCall.groupCall()]() to create the group calling object by passing the group-id and the UI context. 

For example in Java,
```java
mGroupcall = MesiboCall.getInstance().groupCall((MesiboCallActivity) getActivity(), mGid);  
```
### Making a call

Group users participate in the conference by making calls to the group. There are two kinds of call:
 
  - **publish call** to publish self video or voice stream. In subsequent sections, we will refer to this as publishing.
  - **subscribe call** to receive other participants' video or voice stream. In subsequent sections, we will refer to this as subscribing.

A participant can publish multiple streams simultaneously by making multiple publish calls. Similarly, a participant can view other participants' streams by making multiple subscribe calls. There is no limit on these calls.

When a participant makes a publish call (publishing), all the participants will receive a corresponding call object which they can use to make a subscribe call.

### Publish Call
To publish your stream, you need to create a participant before making a call. Call the [GroupCall.publish()]() method to create a local publisher and then use the call method to publish your stream. You can create and publish multiple streams simultaneously, for example, one camera, two screens, one whiteboard, etc.

For example, in Java, to publish stream from the front camera
```java
//Publish self stream
mLocalPublisher = mGroupcall.publish(0);
mLocalPublisher.setVideoSource(MesiboCall.MESIBOCALL_VIDEOSOURCE_CAMERAFRONT, 0);
mLocalPublisher.call(mRoom.audio, mRoom.video, this);
```

### Subscribe Call
To view a publishers stream (either your own stream or a different publisher in the conference), you need to make a subscribe call using the [call()]() method. You can subscribe to audio only, video only, or both audio and video while making a subcribe call.

For example, in Java, to view both audio and video of a participant, place a call as follows 
```java
participant.call(true, true , this);
```

### Overview of Methods in the Participant 
//Put in a table with links

- **call()** To establish a connection to the participant to get the video/audio stream
- **setVideoView()** To display the stream in a UI element
- **getName()** Returns the name of the participant - set using [setName()]() by the publisher
- **getAddress()** Returns the address of the participant
- **getType()** Returns an integer, the streamId - passed to [publish]() by the publisher
- **getId()** Returns a unique integer identifying the stream
- **isMe()** Returns true if participant is a local stream, false otherwise
- **isTalking()** Returns true if participant is talking, false otherwise
- **toggleAudioMute()** To mute the audio of a stream
- **toggleVideoMute()** To mute the audio of a stream
- **getMuteStatus()** To get the mute status of a stream
- **hangup()** Hangup the stream

### Overview of Listeners

Therea are two types of group call listeners.
- [GroupCallListener]() The global group call listener. Implement this to be notified about whenever there is a new [publisher]() or [subscriber]() who has joined the conference call 
- [GroupCallInProgressListener]() listener for the call that you make for viewing a participant. Implement this listener to be notified about mute events,

The following functions are part of the global call listener
- **MesiboGroupcall_OnPublisher** Called when a publisher has joined the conference
- **MesiboGroupcall_OnSubscriber** Called when a participant has subscribed to your stream
- **MesiboGroupcall_OnAudioDeviceChanged** Called when the participant has changed their audio source

The following functions are part of the [GroupCallInProgressListener]() listener class which you need to provide while making a `call`. You can implement a different [GroupCallInProgressListener]() for each call. These listener functions will be called by mesibo to notify you of various actions by the participant that you are viewing that affects the stream. For example, muting audio/video, changes the video/audio source, hangs up, etc.

- **MesiboGroupcall_OnConnected** Called when a participant is connected
- **MesiboGroupcall_OnVideo** Called when you receive the video stream from a participant that you have subscribed to
- **MesiboGroupcall_OnVideoSourceChanged** Called when the publisher has changed the video source
- **MesiboGroupcall_OnAudio** Called when you receive audio stream from a participant
- **MesiboGroupcall_OnMute** Called when a particpipant has muted/unmuted their audio or video
- **MesiboGroupcall_OnTalking** Called when a participant that you have subscribed to is talking
- **MesiboGroupcall_OnHangup** Called when a partipant hangs up

### Viewing a stream
To display a stream, you need to call the [setVideoView()]() method on the participant object. 

For example, in Android, you can have a `MesiboVideoView` in your XML Layout file like below,
```xml
<com.mesibo.calls.api.MesiboVideoView
    android:id="@+id/participant_stream_view"
    android:layout_width="match_parent"
    android:layout_height="match_parent"/>
```
You can display the stream in this view, as follows:
```java
MesiboVideoView video = findViewById(R.id.participant_stream_view); 
participant.setVideoView(videoView);
```

For example in Javascript, If you have an HTML element where you would like to display the stream, like below:
```HTML
<video class="centered" id="video-publisher" autoplay playsinline muted width="100%" height="100%" />
```

You need to call `setVideoView()` as follows
```javascript
p.setVideoView('video-publisher', on_setvideo, 100, 50);

```

In case you are using a DOM framework and the HTML element you need to display the stream is not created at the moment you call `setVideoView()`, you can try reattaching after a small delay or a timeout, which can be specified in `retryTimeout`. You can also specify the maximum number of attempts to attach in `maxRetries`. If the element is not available even after `maxRetries` is reached, `setVideoView()` will fail. In this case, `on_setvideo` will be called with `false`. If `setVideoView()` is successful, it will be called with `true`

Example,
```javascript
function on_setvideo(isSet){
    //isSet is `true` if the video element is displaying the video, false otherwise 
}

//Calls setVideoView every 100ms, for a maximum number of 50 times, until successful 
p.setVideoView('video-publisher', on_setvideo, 100, 50);
```

### Publishing a stream
As a member of the conference room group, you can send your streams to the group, which other members of the group can view. You can publish streams with two types of sources - **camera** and **screen**. To publish your stream, use [MesiboGroupCall.publish()]() 

For example, in Java, to publish stream from the front camera
```java
//Publish self stream
mLocalPublisher = mGroupcall.publish(0);
mLocalPublisher.setVideoSource(MesiboCall.MESIBOCALL_VIDEOSOURCE_CAMERAFRONT, 0);
mLocalPublisher.call(mRoom.audio, mRoom.video, this);
```

For example, in Javascript, If you want to send your camera feed to the group, we set the source as `STREAM_CAMERA` &  make a call as follows:
```javascript
publisher.setVideoSource(STREAM_CAMERA);
publisher.call(true, true, callInProgressListener);
publisher.setName("My camera stream");
```

If you  want to share your screen with the group, we set the source as `STREAM_SCREEN` &  make a call as follows
```javascript
publisher.setVideoSource(STREAM_SCREEN);
publisher.call( true, true, callInProgressListener);
publisher.setName("My screen stream");
```
### Publishing multiple streams
Note that you can simultaneously be publishing as many streams as you like. For example, in a conference, you can share multiple screens at the same time. Or if you have multiple camera devices, you can share multiple feeds at the same time to build a 360 View! 

For every stream you want to publish, initialize a call object using `publish()` with a unique screen-id and then place a `call()` 

On the other end, the members of the group will be able to see multiple streams with different screen-id from you. They will be able to subscribe to each of your streams separately, when [MesiboGroupcall_OnPublisher]() will be called for each of your published streams.

For example, in Javascript, to share multiple screens at once, initialize a call object using `publish()` with an id for each screen you want to share. On the remote end where they receive your stream, they can read this stream-id using `MesiboParticipant.getType()` 

```javascript
// A call object for each stream is required
var screen_1 = gCall.publish(1);
screen_1.setVideoSource(STREAM_SCREEN);

var screen_2 = gCall.publish(2);
screen_2.setVideoSource(STREAM_SCREEN);

var screen_3 = gCall.publish(3);
screen_3.setVideoSource(STREAM_SCREEN);
 
screen_1.call(false, true, callInProgressListener);
screen_2.call(false, true, callInProgressListener);
screen_3.call(false, true, callInProgressListener);
```

### Subscribing to participants
You will get the call object for each participant in [MesiboGroupcall_OnPublisher](), if they are publishing their stream. You can subscribe to a publisher's stream as follows using the `call()` method. 

For example, in Java, if you would like to have both audio and video while viewing a publisher, make a call as follows
```java
public void MesiboGroupcall_OnPublisher(MesiboCall.MesiboParticipant participant, boolean joined) {

    if (joined) {
        participant.call(true, true , this);
    }
}
```

To make an audio only call,
```java
participant.call(mRoom.audio, mRoom.video , this);
```

### Subscribing to multiple streams from a participant 
Each stream published by a participant will have a different stream-id(set by the publisher). You can get the stream-id using the method `getType()`.

For example, let's say we have a participant sharing three screens at once. Now, in this case, `Mesibo_OnParticipants` will contain three participant call objects, all with the same uid (`getId()` will give you the same user-id, since they are from the same user) but different stream-id (`getType()` will give you the stream-id that was set by the publisher for that stream).

So, if we want to display these multiple screens from the same participant at once, we need to have different HTML elements. Then our call function will look something like below:
```javascript
var element_id = 'video-stream-'+ p.getId()+'-type-'+ p.getType();
p.call(true, true, callInProgressListener);
```

For example, suppose we have a participant with a user-id `1234` and they are sharing three screens in a conference. Each screen will have a different stream-id set by the publisher. Suppose the stream_ids are  `1`, `2`, `3`. If we want to display all of them simultaneously, we need to have three HTML elements.

```html
<video id="video-stream-1234-type-1" autoplay playsinline muted/>
<video id="video-stream-1234-type-2" autoplay playsinline muted/>
<video id="video-stream-1234-type-3" autoplay playsinline muted/>
```

Let's say we got three call objects in `Mesibo_OnParticipants` as `p1`, `p2`, `p3`. Then :
```
p1.getId() == p2.getId() == p3.getId() == 1234

p1.getType() == 1, p1.getType() == 2, p1.getType() == 3
```

To subsctibe to each of the streams, we need to use `call()` on each call object like below:
```javascript
p1.call(true, true, callInProgressListener);
p2.call(true, true, callInProgressListener);
p3.call(true, true, callInProgressListener);
```
and then call `setVideoView()` on these different HTML elements to display them.

### Muting Streams

We can mute video and audio locally, for the streams that we are viewing. You can use the [toggleAudioMute]()  method to toggle the audio and [toggleVideoMute]() method to toggle the video of a stream. 


When you mute your stream, other participants viewing your stream, will be notified of it through `MesiboGroupcall_OnMute`
```java
public void MesiboGroupcall_OnMute(MesiboCall.MesiboParticipant participant, boolean audioMuted, boolean videoMuted, boolean remote) {
    // Remote participant has muted 
    // Check mute status
    if(participant.getMuteStatus(true)){
    	// Video Muted
    }

    if(participant.getMuteStatus(false)){
    	// Audio Muted
    }
}
```

### Handling Mute 
You can get the mute status of a stream with [getMuteStatus()](). When a participant mutes/unmutes audio or video, people viewing the participant will be notified of it through [MesiboGroupcall_OnMute]() 

### Talking Indicator
You can get the talking status of a stream with [isTalking](). When a participant starts/stops talking, people who are viewing the publisher will be notified of it through [MesiboGroupcall_OnTalking]()  

### Hanging up a stream
To hangup a stream, you need to use [hangup()](). It will hangup. When a participant hangsup, people who are viewing that publisher will be notified of it through [MesiboGroupcall_OnHangup]()

