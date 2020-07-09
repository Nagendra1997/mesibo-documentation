---
title: Write your First mesibo Enabled Application
description: Write your First mesibo Enabled Application
keywords: mesibo, android, ios
---
{% include_relative nav.html selected="files" %}

In this part, we will learn about sending push notifications to your application from mesibo. 

## Prerequisites
You MUST go through the following prerequisites before you read further.

- Read the [Preparation]({{ '/documentation/tutorials/first-app/' | relative_url }}) Guide.
- Read one of the Android, iOS, JS section of this tutorial.

## Push notification in mesibo

A notification is a message you can display to the user outside of your application's normal UI.

There are two kinds of push notification:
1. Background notification: This type of notifications are not visible (data type in Android and background or VoIP type in iOS). 
2. Alerts: These are visible alerts(notification type in Android and alert type in iOS).

By default, mesibo sends background notification that wakes your app in the background, and then the application can start mesibo to receive all the messages. This is a recommended way of receiving push for real-time application as it gives you the finer control. However, You can change this default behavior from the mesibo console by disabling the Background push checkbox. It will then send visible push alerts.
 
## Setting up Push notification Credentials in mesibo Console

For Android(FCM Notifications ) and iOS(APNs or VoIP) push notification, you need to set up the push notification credentials in the mesibo console. This way mesibo will be able to send push notifications to your users. 

Follow the steps below to configure your push notification credentials

### Configure FCM Credentials for Android Notifications 

1. Get the Firebase Sender ID and Firebase Server Key from [Firebase Console](https://firebase.google.com/console) of your app. Enter it in the mesibo console. Refer to the [Firebase Documentation](https://firebase.google.com/docs/cloud-messaging/android/first-message) and [FCM Messages](https://firebase.google.com/docs/cloud-messaging/concept-options) for setting up your FCM Server.
2. Go to your app settings in [mesibo console](https://mesibo.com/console/). Go to the section `Push Notifications`
3. Enter the credentials that you got in the Android - Firebase Cloud Messaging (FCM) Configuration section. 
4. Under push, settings select Background Push (recommended) or set your required push notification type.

### Configure APN/VoIP Credentials for iOS Notifications 

1. Get the  universal Push/VoIP certificate in .p12 format from your [Apple Developer Account](developer.apple.com). Refer to [Obtain a Provider Certificate from Apple](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/establishing_a_certificate-based_connection_to_apns) 
2. Go to your app settings in [mesibo console](https://mesibo.com/console/). Go to the section `Push Notifications`
3. Choose the universal Push/VoIP certificate file that you downloaded earlier and upload it in the section Apple Push Notification (APN) Configuration. 
4. Under push, settings select Background Push (recommended) or set your required push notification type.

## Sending push notification to your users

Before you go ahead, ensure that you have set up the correct credentials for Android/iOS and have made the required push notification settings in the mesibo console following the steps described in the previous section.

### Sending push notifications to users in Android 

1. For each user, you need to get a client push token or device token. To understand how to get the user push token refer [Firebase Docs](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register) or this detailed [tutorial on Android Push Notifications](https://medium.com/@nileshsingh/how-to-add-push-notification-capability-to-your-android-app-a3cac745e56e) 

2. Now that you have your client push token, register client push token using Mesibo `setPushtoken()` API, and then you can send your notifications. 

Refer to the Messenger App Source code for Android to understand how to do this.
- [Example code in Java](https://github.com/mesibo/messenger-app-android-beta/blob/4a60e57fea3676100aab2c1c5ce58c74a0f621e3/app/src/main/java/org/mesibo/messenger/SampleAPI.java)

- [Example code in Kotlin](https://github.com/mesibo/messenger-app-android-kotlin/blob/9e5d8f5480b209958f090dd16085e89a15614b6b/app/src/main/java/org/mesibo/messenger/SampleAPI.kt)


### Sending push notifications to users in iOS 

1. For each user, you need to get a client push token or device token. To understand how to get the user push token refer [APN Docs](https://developer.apple.com/documentation/usernotifications/registering_your_app_with_apns) or this detailed [tutorial on iOS Push Notifications](https://www.raywenderlich.com/8164-push-notifications-tutorial-getting-started)

2. Now that you have your client push token, register client push token using Mesibo `setPushtoken()` API, and then you can send your notifications. 

Refer to the Messenger App Source code for iOS to understand how to do this.
- [Example code in Objective-C](https://github.com/mesibo/messenger-app-ios/blob/f89f477c687a6143b643402c70f4d61623bf2369/MesiboApplication/SampleAPI.m)

- [Example code in Swift](https://github.com/mesibo/messenger-app-ios-swift/blob/5daf021dfd4eee2141c8e5a8a6f9bc9eebe31de3/MesiboApplication/PushKitNotify.swift)

If you are facing any issues setting up push notifications for your app refer to the [Push Notification FAQ](https://mesibo.com/documentation/faq/webhooks-and-push-notifications/#i-am-not-able-to-receive-push-notifications-on-the-app-what-should-i-do) or raise a support request from your console. 

[On to Part 9 >>](ui-modules.md){: class="button outline-btn" style="margin-bottom: 30px; margin-right: 100%"}
