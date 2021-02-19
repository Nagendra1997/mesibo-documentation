---
description: Listeners used in the conferencing API 
keywords: listeners, conferencing, voice, video
title: Mesibo Conferencing APIs - Listeners 
---

The Mesibo Conferencing API provides the following listeners:

- [GroupCallListener](#groupcalllistener), `MesiboCall.GroupCallListener` provides listeners that inform you about participants joining and leaving the conference.   

- [GroupCallInProgressListener](#groupcallinprogresslistener), `MesiboCall.GroupCallInProgressListener` allows listeners to handle the stream that you are receiving from a participant such as, mute, hang up, talk detection, etc. 

## GroupCallListener 
Your conferencing app needs to implement the `GroupCallListener` so that you will be notified when participants join and leave the conference. When participants join the conference, you can subscribe to their stream and view them. 

The following are the listeners available in `MesiboCall.GroupCallListener`:
 
### MesiboGroupcall_OnPublisher 
When a new participant joins the group call and starts publishing their stream, mesibo will call `MesiboGroupcall_OnPublisher`. Here, depending on your conferencing app logic, you can decide whether to view them or not. If you decide to view a publisher, you can subscribe to them using [MesiboParticipant.call()](). But, it is not necessary that you place the `call()` immediately. Say you are developing a Zoom like meeting app. Here, all members in the meeting need to view every other member's stream. So, you need to use `call()` for every publisher that joins. You can also add publishers into a list and then ask the user to choose whether to view a publisher's stream or not. If and when the user wishes to view the stream, you can then place a `call()`

Say you are developing a virtual classroom app. Here, only teachers can publish the stream and others only need to view it. Of course, you can set [conference publish permissions]() to ensure that student users can only view the stream. But, you can also check here that you only subscribe to select streams.

Parameters
- **participant** - The publisher, a [MesiboParticipant]() object which you can use to make a `call()` to view their stream. Contains the name, address, ID, etc of the participant.
- **joined** - `true` if they have started publishing, `false` when they stop publishing.

For example, in Java,
```java

public void MesiboGroupcall_OnPublisher(MesiboCall.MesiboParticipant participant, boolean joined) {

    if (joined) {
        // call the publisher to view their stream
	// Add participant to list of publishers
    } else {
	// Remove participant from list of publishers	
    }
}
```

### MesiboGroupcall_OnSubscriber 
When a new participant joins the group call and starts viewing your stream, mesibo will call `MesiboGroupcall_OnSubscriber`. For example, say you are building a webinar app, where you are the only speaker. You need to see who has subscribed to your webinar stream. In this case, only you are publishing. Everyone else is simply viewing it by subscribing to your stream. 

Mesibo will call `MesiboGroupcall_OnSubscriber` for every person who is viewing you, with the following parameters: 

- **participant** - The subscriber, a [MesiboParticipant]() object which contains the name, address, etc of the subscriber
- **joined** - `true` when they have joined the conference and have started viewing you, `false` when they stop viewing you 

For example, in Java
```java
public void MesiboGroupcall_OnSubscriber(MesiboCall.MesiboParticipant p, boolean joined) {
        // This participant is viewing your stream
}
```

### MesiboGroupcall_OnAudioDeviceChanged 
`MesiboGroupcall_OnAudioDeviceChanged` is called when the active audio device is changed by a user action, for example, headset inserted, Bluetooth was turned ON, active audio device changes from an earpiece to a speaker, etc while you are in an active group call.

Parameters:
- **newAudioDevice,**, The new [AudioDevice]() that you the switched to and is considered to be active.
- **preAudioDevice**, The previous [AudioDevice]() that was active before you switched to a different one. The previous device is now considered to be inactive.

For example, In Android,
```java
public void MesiboGroupcall_OnAudioDeviceChanged(MesiboCall.AudioDevice newAudioDevice, MesiboCall.AudioDevice prevAudioDevice) {
	// prevAudioDevice -> newAudioDevice
}
```

## GroupCallInProgressListener
The `GroupCallInProgressListener` is invoked by mesibo for various events for a participant in an ongoing group call, such as when the participant mutes, hangs up, starts/stops talking, starts to stream audio and video, etc.

Using these callbacks, you get continuous feedback on what is happening on participant's stream. If you are displaying the stream, and you can update your UI using this information. For example, if the participant has muted their audio then you may show an icon that indicates that the audio has been muted. 

Note, that all the listeners in `GroupCallInProgressListener` will be called only for those publishers that you have subscribed to using [call()](). When you "subscribe" to a publisher, you are informing mesibo that you would like to view this publisher and you are "subscribing" to stream events for this publisher. So, mesibo will let you know of these "stream events" through `GroupCallInProgressListener`. 

The following are the listeners in `MesiboCall.GroupCallInProgressListener` which will help you handle stream events. 

### MesiboGroupcall_OnMute 

`MesiboGroupcall_OnMute` is called when a participant mutes their audio or video. Note that, you can use [getMuteStatus()]() to know about the current mute status of a participant. 

Parameters:
- **participant**, The participant who you are viewing - a [MesiboParticipant]() object
- **audioMuted**, `true` if the participant has muted their audio, `false` otherwise
- **videMuted**, `true` if the participant has muted their video(ie; they are not publishing their video), `false` otherwise

For example, In Android,
```java
public void MesiboGroupcall_OnMute(MesiboCall.MesiboParticipant participant, boolean audioMuted, boolean videoMuted, boolean remote) {
    // Remote participant has muted
    // Check mute status
    if(audioMuted){
        // Audio Muted
    }

    if(videoMuted){
        // Video Muted
    }
}
```

### MesiboGroupcall_OnHangup 
`MesiboGroupcall_OnHangup` is called when the remote participant hangs up.

Parameters:
- **participant**, The participant that hanged up, A [MesiboParticipant]() object 
- **reason**, The reason for hangup indicated by any one of the following:
  - `MESIBOCALL_HANGUP_REASON_REMOTE`, The remote participant hanged up 
  - `MESIBOCALL_HANGUP_REASON_BACKGROUND`, The application moved into the background 
  - `MESIBOCALL_HANGUP_REASON_USER`, The user hung up the participant 
  - `MESIBOCALL_HANGUP_REASON_ERROR`, An error occurred 

For example, in Android,

```java
public void MesiboGroupcall_OnHangup(MesiboCall.MesiboParticipant p, int reason) {
	if(MESIBOCALL_HANGUP_REASON_REMOTE == reason){
		// Remote user has hanged up
		// Cleanup
	}
``` 

### MesiboGroupcall_OnConnected 

`MesiboGroupcall_OnConnected` is called when connection to a publisher is established, after you subscribe to them and when the publisher is temporarily disconnected.

Parameters:
- **participant**, The particpant who got connected/disconnected, A [MesiboParticipant]() object
- **connected**, `true` if connected, `false` otherwise


For example, in Java,
```java
public void MesiboGroupcall_OnConnected(MesiboCall.MesiboParticipant participant, boolean connected) {
    if(connected){
        // Participant is connected
    } else {
        // Participant has been disconnected
    }
}
```
### MesiboGroupcall_OnTalking 

`MesiboGroupcall_OnTalking` is called when the participant starts or stops talking. Note, that you can use [isTalking()]() to check if a participant is talking. 

Parameters:
- **participant**, The talking participant, A [MesiboParticipant]() object 
- **talking**, Set to `true` if the participant has started talking. `false` if they stopped talking. 

```java
public void MesiboGroupcall_OnTalking(MesiboCall.MesiboParticipant participant, boolean talking) {
        if(participant.isTalking()){
                // Handle talking. Example, Show an indicator
        }
    }
```
### MesiboGroupcall_OnVideoSourceChanged 

`MesiboGroupcall_OnVideoSourceChanged` is called when the publisher changes their video source. For example, from camera feed to sharing their screen. Or from their front-facing camera to rear-facing camera (on Mobile) 

A **video source** can be one of:
- `MESIBOCALL_VIDEOSOURCE_CAMERADEFAULT` Default camera to publish from
- `MESIBOCALL_VIDEOSOURCE_CAMERAFRONT` to publish stream from the device's front camera (on Mobile)
- `MESIBOCALL_VIDEOSOURCE_CAMERAREAR` to publish stream from the device's rear camera (on Mobile)
- `MESIBOCALL_VIDEOSOURCE_SCREEN` to publish live screen

Parameters:
- **newVideoSrc**, The current active video source 
- **prevVideoSrc**, The previous video source

For example, in Android
```java
public void MesiboGroupcall_OnVideoSourceChanged(int newVideoSrc, int prevVideoSrc) {
	if(MesiboCall.MESIBOCALL_VIDEOSOURCE_SCREEN == newVideoSrc
		&& MesiboCall.MESIBOCALL_VIDEOSOURCE_CAMERAFRONT == prevVideoSrc){
		// The publisher has switched to streaming their screen, 
		// Previously they were streaming from their camera
		... 
	}

}
```
### MesiboGroupcall_OnVideo 

`MesiboGroupcall_OnVideo` is called when you receive video from a participant who you have subscribed to. It is also called when any aspect of the video changes. For example, changes in aspect ratio, if video changes to landscape, etc. You can update the UI used to display the video accordingly for such changes. 

Parameters:
- **participant**, The participant who is publishing the video, A [MesiboParticipant]() object 
- **aspect_ratio**, The ratio of width to height of the video
- **available**, `true` if video available, `false` otherwise

For example, in Java
```java
public void MesiboGroupcall_OnVideo(MesiboCall.MesiboParticipant participant, float aspect_ratio, boolean available) {
	// Display the video
}
```
 
### MesiboGroupcall_OnAudio

`MesiboGroupcall_OnAudio` is called when you receive audio from a participant who you have subscribed to. 

Parameters:
- **participant**, The participant who is publishing the video, A [MesiboParticipant]() object 

For example, in Java
```java
public void MesiboGroupcall_OnAudio(MesiboCall.MesiboParticipant mesiboParticipant) {
	// Receiving Audio
}
```
