---
description: Mesibo Conferencing APIs - MesiboParticipant Class Reference
keywords: conferencing, participant, voice, video 
title: Mesibo Conferencing APIs - MesiboParticipant
---

The following is a list of methods available in `MesiboParticipant` class. You get a `MesiboParticipant` object at two places:
1. When you publish a stream using [GroupCall.publish]() 
2. Through [GroupCallListener]() callbacks, [MesiboGroupcall_OnPublisher]() and [MesiboGroupcall_OnSubscriber]()

You should not directly create an instance of `MesiboParticipant`.

## Stream Handling Functions

### hangup
Stop publishing or viewing the stream.

It does not take any parameters.

For example, in Android,
```java
participant.hangup();
```

### mute
Mute or unmute voice and/or video of a participant

It takes the following parameters:

- **audio**, audio mute/unmute operation
- **video**, video mute/unmute operation
- **enable**, Pass true to mute, false to unmute.

For example, in Android, to mute audio
```java
participant.mute(true, false, true)
```

## Video Handling Functions

### setVideoView
Set the video view for displaying video from a participant 

It takes the following parameter:
- **mesiboVideoView**, An instance of `MesiboVideoView` to display the video

For example, in Android, you can have a `MesiboVideView` in your XML Layout file like below,
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

In the Javascript API, `setVideoView()` takes the following parameters:
- **videoView**, ID of the HTML element where you want to display the stream
- **on_setvideo**, function that will be called when `setVideoView()` is completed
- **retryTimeout** - Time in milliseconds, after which attach will be called again if the previous attach failed
- **maxRetries** - Maximum number of retries to attach

If you have an HTML element where you would like to display the stream, like below:
```HTML
<video class="centered" id="video-publisher" autoplay playsinline muted width="100%" height="100%" />
```

You need to call `setVideoView()` as follows
```javascript
function on_setvideo(set){
}

participant.setVideoView('video-publisher', on_setvideo, 100, 50);
```
In case you are using a DOM framework and the HTML element you need to display the stream is not created at the moment you call `setVideoView()`, you can try reattaching after a small delay or a timeout, which can be specified in `retryTimeout`. You can also specify the maximum number of attempts to attach in `maxRetries`. If the element is not available even after `maxRetries` is reached, `setVideoView()` will fail. In this case, `on_setvideo` will be called with `false`. If `setVideoView()` is successful, it will be called with `true`

Example,
```javascript
function on_setvideo(set){
    //set is `true` if the video element is displaying the video, false otherwise
}

//Calls setVideoView every 100ms, for a maximum number of 50 times, until successful
p.setVideoView('video-publisher', on_setvideo, 100, 50);
```

### getVideoView()
Returns a `MesiboVideoView` object, if set using `setVideoView()`

It does not take any parameters.

For example, in Android,
```java
MesiboVideoView mv = particpant.getVideoView()
```

### setVideoSource
Set the video source while publishing a video. 

It takes the following parameters:

- **videoSource** , Video source type specified by a [VideoSource]() constant. Can be one of
  - `MESIBOCALL_VIDEOSOURCE_CAMERADEFAULT` Default camera to publish from
  - `MESIBOCALL_VIDEOSOURCE_CAMERAFRONT` to publish stream from the device's front camera (on Mobile)
  - `MESIBOCALL_VIDEOSOURCE_CAMERAREAR` to publish stream from the device's rear camera (on Mobile)
  - `MESIBOCALL_VIDEOSOURCE_SCREEN` to share live screen
- **sid** 

For example, in Android,
```java
mLocalPublisher.setVideoSource(MesiboCall.MESIBOCALL_VIDEOSOURCE_CAMERAFRONT, 0);
```

### switchCamera
Switch between the back-facing camera and front-facing camera.

It does not take any parameters.

For example, in Android,
```java
participant.switchCamera();
```
### switchSource
Switch between camera and screen. 

It does not take any parameters.

For example, in Android,
```java
participant.switchSource();
```
### getVideoSource
Returns the active video source. Can be one of
- `MESIBOCALL_VIDEOSOURCE_CAMERADEFAULT` Default camera to publish from
- `MESIBOCALL_VIDEOSOURCE_CAMERAFRONT` to publish stream from the device's front camera (on Mobile)
- `MESIBOCALL_VIDEOSOURCE_CAMERAREAR` to publish stream from the device's rear camera (on Mobile)
- `MESIBOCALL_VIDEOSOURCE_SCREEN` to publish live screen

It does not take any parameters.

For example, in Android,
```java
participant.getVideoSource()
```

### toggleVideoMute
Toggle mute the video. That is, unmute the video if it is muted and mute it otherwise. While publishing, it stops video capture through the camera or screen, if the video is muted.

It does not take any parameters.

For example, in Android,
```java
participant.toggleVideoMute();
```

### getAspectRatio
Returns the width to height ratio of the video as a float value.

It does not take any parameters.

For example, in Android,
```java
participant.getAspectRatio();
```

### isVideoLandscape
Returns `true`, if the video is landscape(ie; width of the video is greater then height, aspect ratio is > 1) `false` otherwise.

It does not take any parameters.

For example, in Android,
```java
participant.isVideoLandscape()
```

### hasVideo 
Returns `true`, if the stream has a video,`false` otherwise

It does not take any parameters.

For example, in Android,
```java
participant.hasVideo()
```

### changeVideoFormat
Takes the following parameters:
- **i**
- **i1**
- **i2**


## Voice Handling Functions

### toggleAudioMute
Toggle mute the audio. That is, unmute the audio if it is muted and mute it otherwise.

It does not take any parameters.

For example, in Android,
```java
participant.toggleAudioMute();
```
### hasAudio
Returns `true`, if the stream has audio,`false` otherwise

It does not take any parameters.

For example, in Android,
```java
participant.hasAudio()
```

## Stream Status/Information Functions

### getMuteStatus
`getMuteStatus` Returns the current mute status of the stream. Whenever a participant mutes the audio/video the mute status will be updated.
 
It takes the following parameters:

- **video**, Pass true to check video mute status, false for voice mute status.

For example, in Android, to check if a participant has muted their audio
```java
boolean m = participant.getMuteStatus(false)
```
Note, that When a remote participant mutes [MesiboGroupcall_OnMute]() will be called.

### getTalkTimstamp
Returns the last timestamp at which the user talked.

It does not take any parameters.

For example, in Android,
```java
long lastTalked = participant.getTalkTimstamp();
```
### getId
Returns the unique ID of the participant 

It does not take any parameters.

For example, in Android,
```java
participant.getId();
```

### getName
Returns the name of the participant 

It does not take any parameters.

For example, in Android,
```java
participant.getName();
```

### getAddress
Returns the address of the participant 

It does not take any parameters.

For example, in Android,
```java
participant.getAddress();
```

### setName 
Set the name of the participant. For example, while publishing a stream you can set your name, as the name of the publisher.

It takes the following parameter.
- **name**, Name of the participant 

For example, in Android,
```java
participant.setName("John Doe");
```
### isCallConnected
Returns true if the participant is connected on the call, false otherwise.

It does not take any parameters.

For example, in Android,
```java
participant.isCallConnected();
```

### isCallInProgress
Returns true if the call to the participant is in progress, false otherwise.

It does not take any parameters.

For example, in Android,
```java
participant.isCallInProgress();
```

### isVideoCall
Returns `true`, if video call `false` otherwise

It does not take any parameters.

For example, in Android,
```java
participant.isVideoCall()
```

### isMe
Returns `true`, if particpant is self, `false` otherwise

It does not take any parameters.

For example, in Android,
```java
participant.isMe()
```

### isFrontCamera
Returns `true`, if particpant is publishing from their front camera(on mobile), `false` otherwise

It does not take any parameters.

For example, in Android,
```java
participant.isFrontCamera()
```

### isTalking()
Returns `true`, if particpant is talking, `false` otherwise

It does not take any parameters.

For example, in Android,
```java
participant.isTalking()
```

## Call Functions

### call
When you need to view a publisher, that you get in [MesiboGroupcall_OnPublisher](),you need to use `call()` to subscribe to their stream. 

The `call()` method takes the following parameters:
- **audio**, `true` if you would like to receive the audio stream, `false` otherwise
- **video**, `true` if you would like to receive the video stream, `false` otherwise
- **groupCallInProgressListener**, An instance of [GroupCallInProgressListener]() 

For example, in Java, if you would like to have both audio and video while viewing a publisher, make a call as follows
```java
public void MesiboGroupcall_OnPublisher(MesiboCall.MesiboParticipant participant, boolean joined) {

    if (joined) {
        participant.call(true, true , this);
	// If you have implemented GroupCallInProgressListener in "this" context
    }
}
```

To make an audio only call,
```java
participant.call(mRoom.audio, mRoom.video , this);
```

While publishing your stream, by calling [publish()], you later need to use `call()`.

For example, in Java, to publish a video stream from the front camera along with your audio
```java
//Publish self stream
mLocalPublisher = mGroupcall.publish(0);
mLocalPublisher.setVideoSource(MesiboCall.MESIBOCALL_VIDEOSOURCE_CAMERAFRONT, 0);
mLocalPublisher.call(true, true, this);
```

### setListener
Set the listener instance which will be invoked for changes in the stream

Takes the following parameter.
- **listener**, An instance of [GroupCallInProgressListener]()

For example, in Android,
```java
participant.setListener(this)
// If you have implemented GroupCallInProgressListener in "this" context
```
## Utility Functions

### setUserData
Store any object - can be an object of a user defined class, etc. Once set, you can access this object using [getUserData](#getuserdata)

Takes the following parameter.
- **object**, Any Object

For example, in Android,
```java
participant.setUserData(myData);
```

### getUserData
Access the data, set using [setUserData](#setuserdata)

It does not take any parameters.

For example, in Android,
```java
MyData mData = (MyData)participant.getUserData();
```
