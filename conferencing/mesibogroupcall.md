---
description: Conferencing APIs - MesiboGroupCall 
keywords: call, voice, video 
title: Conferencing APIs - MesiboGroupCall 
---

`MesiboCall.MesiboGroupCall` controls various aspects of a group call, like joining and leaving the conference call, playing in call sound, etc. 
The following is a list of methods available in `MesiboGroupCall` API. To first step in starting a group call, is to create a `MesiboGroupCall` object. 

### Creating an instance of MesiboGroupCall
 
You can get a group call instance using [MesiboCall.groupCall()](). 

Before you make a group call,  ensure the following :
1. Created a group and obtained a group-id. See [Creating a group to hold the conference]()
2. Added members to the group. See [Adding Members to the conference group]() 
3. Initaialized and started mesibo, using a "user access token". The mesibo user placing the group call, must be a member of that group. 
4. Granted the required device permissions for using mesibo Call APIs. See [checkPermissions]().

[MesiboCall.groupCall()]() takes the following parameters:

- **callActivity**, A [MesiboCallActivity]() instance
- **groupid**, The groupid that you obtained after creating a group 

and returns a [MesiboCall.MesiboGroupCall]() object. Returns `null` if there already is an ongoing call. 

For example, in Java
```java
mGroupcall = MesiboCall.getInstance().groupCall((MesiboCallActivity) getActivity(), 9999)
```

Once you create a group call instance, you can join the call using [MesiboGroupCall.join()](#join) and leave the call using [MesiboGroupCall.leave()](#leave)

## MesiboGroupCall Methods 

### join 
This initiates the group call and you join the call. This MUST be the first function you should call once your user-interface for displaying the group call is ready.

It takes the following parameters:

- **GroupCallListener**, [GroupCallListener]()

For example, in Android,
```java
mGroupcall.join(this);
// If you have implemented GroupCallListener in "this" context
```

### leave 
All the streams that you are publishing  will be hanged up and you  will be exited from the conference room. 

It does not take any parameters.

For example, in Android,
```java
mGroupcall.leave();
```

## Utility Functions

### playInCallSound
You can use this utility function to play sound when a participant joins or leaves the conference call.

It takes the following parameters:

- **context**, Application context.
- **audio file**, In Android, pass the resource-ID of the audio that you need to play. In iOS, provide the path to the audio file is an `NSUrl`.- **enable**, Set to `true`, to enable playing the sound. 

For example, in Android,
```java
public void MesiboGroupcall_OnPublisher(MesiboCall.MesiboParticipant participant, boolean joined) {
	if (joined) {
		mGroupcall.playInCallSound(getContext(), R.raw.join, false);
		// Plays the audio file located at .../res/raw/join.mp3
	}
}
```

### stopInCallSound
Stops the in-call sound. 

It does not take any parameters.

For example, in Android,
```java
mGroupcall.stopInCallSound();
```

