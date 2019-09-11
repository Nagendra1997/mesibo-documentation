# Mesibo React Native Sample App
This repo contains the source code for a Mesibo sample App using React Native

[mesibo](https://mesibo.com) allows you to quickly add real-time messaging, voice and video calling into your mobile Apps, and Websites.
  - Enable 1-to-1 messaging, group chat, or add a chatbot in your apps or website
  - Add high quality voice chat between users
  - Adding real-time video calling in your apps

[React Native](https://facebook.github.io/react-native/) uses React to build native apps.

React native is a framework developed by Facebook and it's like react, but it uses native components instead of web components as a building blocks form. React native is a way to develop mobile apps using React and JavaScript. It means React native is cross-platform, in which you can create one mobile app and run on multiple platforms like Android, iOS, windows, blackberry.

React Native supports these operating system versions:

    Android 4.1 (API 16) and higher
    iOS 8.0 and higher

React Native relies fully on JavaScript and uses Bridge to connect with the native platform. Making a React Native component and the native platform connect via Bridge is probably the trickiest part of the integration process. 


# How does it work?

Mesibo provides real time APIs for messaging,voice and video call which can be easily integrated into any application on Android or iOS platforms. In this sample app, the user interface is developed using ReactNative which interacts with Mesibo to send messages and make audio/video calls.

Integrating React Native into an existing application is a great way to increase the speed of development, make more complex apps, and implement the same feature for both iOS and Android simultaneously.

Here’s what you need to do to integrate React Native components into your Android app:

- Set up React Native dependencies and the directory structure.
- Develop your React Native components in JavaScript.
- Add a ReactRootView to your Android app.
- Start the React Native server and run your native app.
- Verify that the React Native aspect of the app works as expected.

### Connect Mesibo with React-Native

- The ReactNative portion of the app sends commands to  its host to perform actions.Here ,the host is Mesibo which controls ,the iOS or Android portion of the app, over a bridge.For example ,to send a message you just need to enter the access token for your application and the destination user address.

- Mesibo listens and recieves the information about the action to be performed. In the case of sending a message, it will recieve a "Send Message" command from the React-Native component ,upon which Mesibo calls into any number of platform-specific APIs—using the native programming language to send a message to the destination user address entered—and sends a response back to the client.



There are 4 important threads in each React Native application:

1) UI Thread- Known as main Thread. It handles displaying the elements of native Android and iOS UI rendering.

2) JS Thread- JavaScript thread deals with the business logic of the application. e.g.- where JavaScript code is executed, API calls are made, touch event processed and many other logics.

To maintain good performance, JS Thread sends batched updates to UI thread before next frame rendering deadline.

3) Native Modules Thread- sometimes some app needs access to platform API, and then use native modules thread to solve this problem.

4) Render Thread - This is Only in Android L (5.0), React Native render thread is used to generate OpenGL commands used to draw your UI.

### How do threads interact?

Between UI thread and JS threads, we use the bridge to interact. Bridge work as Asynchronous, Batched, Serializable.

Asynchronous - It enables asynchronous communication between the threads.

Batched - It transfers messages from one thread to the other in an optimized way. They transfer message as a block form.

Serializable-The two threads are different from each other and work is also so different and never share or operate with the same data. Instead, they exchange serialized messages.



### List of Samples

* **MesiboSample** This is a sample mobile application that demonstrates how to use mesibo SDK with ReactNative to:
  + send and receive 1-to-1 messaging, group chat
  + make high quality voice and video calls

### Compiling sample code and setup
#### Requirements
* [mesibo account](https://mesibo.com) to get your own API KEY.
* For Android App - latest Android Studio or Gradle if you prefer CLI.
* For iOS App - latest xCode (10.x or later)
* For JavaScript Apps - [https://mesibo.com/mesiboapi.js](https://mesibo.com/mesiboapi.js)
* To install ReactAntive [refer](https://facebook.github.io/react-native/docs/getting-started)

### Backend Setup
Sample backend source code and database schema is available in [php](php/) folder. We recommend you to run it on your own server. However, in case you decide to use the demo API link provided in the source code (SampleAppConfiguration.java or SampleAppConfiguration.m), select a unique namespace in application configuration class to avoid conflict with other testers.

For running backend on your own server, following steps are required
* Create your own mesibo account to get mesibo API key and App token. You may also read [tutorial](https://mesibo.com/documentation/tutorials.html) on how to get mesibo API key and App token.
* Create database and table using schema in [php/SampleApp.sql](https://github.com/mesibo/samples/blob/master/php/sample-app.sql)
* edit config.php and enter API Key, App token, and database credentials.
* change apiUrl in respective application configuration classes.

# Mesibo ReactNative Sample App Source Code
This repository has contains the sample app that demonstrate various aspects of the mesibo SDK for Android with ReactNative. The sample code is reasonably well-documented and we suggest you to read comments to quickly understand the code. The entire documentation for the mesibo SDK is available [here](https://mesibo.com/documentation/introduction.html).

[![App Store](http://imgur.com/y8PTxr9.png "App Store")](https://itunes.apple.com/us/app/mesibo-realtime-messaging-voice-video/id1222921751)   [![Play Store](http://imgur.com/utWa1co.png "Play Store")](https://play.google.com/store/apps/details?id=com.mesibo.mesiboapplication)

# Features!

  - One-on-one messaging and Group chat
  - High quality voice and video calling
  - Rich messaging (text, picture, video, audio, other files)
  - Location sharing
  - Message status and typing indicators
  - Online status (presence) and real-time profile update
  - Push notifications and many more...

## Community
- [Facebook](https://www.facebook.com/mesiboapi)
- [Twitter](https://twitter.com/mesiboapi)
- [LinkedIn](https://www.linkedin.com/company/mesibo)
- [YouTube](https://www.youtube.com/channel/UCxpcg-RSf2-lK4uyysWSsKQ)

### Want to contribute or need to see some improvements?
We would love that, please create an issue or send a PR.


