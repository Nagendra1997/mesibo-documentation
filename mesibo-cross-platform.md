## Cross Platform Application Development With Mesibo

[Mesibo](https://mesibo.com) allows you to quickly add real-time messaging, voice and video calling into your mobile Apps, and Websites.
  - Enable 1-to-1 messaging, group chat, or add a chatbot in your apps or website
  - Add high quality voice chat between users
  - Adding real-time video calling in your apps

Mesibo APIs are provided as a Native SDK for Android, iOS and Web . Because of this, Mesibo can be integrated into any application on a platform of your choice by interfacing with the native API. 

## Building applications with frameworks
With frameworks and libraries like React-Native, Flutter, Swift etc there are a lot of options to choose from which can help you build applications quickly, also ensuring native performance.

You can add communication capabilities to your native application with Mesibo. To integrate with a framework on top of this platform you need to connect or interface with the native SDK. All popular frameworks support interfacing with native code. The general idea is to provide a channel or a bridge that connects the UI part built using the framework and application logic part built using the Native SDK. 

## Interfacing Native SDK in Android

## Interfacing Native SDK in iOS


You can refer to the Official Documentation of the framework that you are using on how to call native code from your application. In the following sections we look at integrating Mesibo with some of the popular frameworks. Sample applications and documentation for is also provided using which you can quickly get started with using Mesibo in your apps.

## React Native
[React Native](https://facebook.github.io/react-native/) is a way to develop mobile apps using React and JavaScript.
To integrate React Native with Mesibo you need to create a native module that acts as a bridge between Mesibo and React-Native.

Refer to the sample application tutorial for integrating [React Native with Mesibo](https://github.com/mesibo/samples/tree/master/react-native/helloworld)

## Flutter
[Flutter](https://flutter.io/) by Google is a new framework that allows us to build beautiful native Apps on iOS and Android from a single codebase. To integrate Flutter with Mesibo Android SDK you need to use [platform channels](https://flutter.dev/docs/development/platform-integration/platform-channels).

The user interface is developed using Flutter which interacts with Mesibo Native API.

The Flutter portion of the app sends commands to its host to perform actions.Here ,the host is Mesibo which controls ,the iOS or Android portion of the app, over a platform channel.For example ,to send a message you just need to enter the access token for your application and the destination user address.
Mesibo listens on the platform channel, and receives the information about the action to be performed. In the case of sending a message, it will receive a "Send Message" command from flutter ,upon which Mesibo calls into any number of platform-specific APIs—using the native programming language to send a message to the destination user address entered—and sends a response back to the client, the Flutter portion of the app.

Refer to the sample application tutorial for integrating [Flutter with Mesibo](https://github.com/mesibo/samples/tree/master/flutter).

## Swift
[Swift](https://developer.apple.com/swift/) is a fantastic way to write software, whether it’s for phones, desktops, servers, or anything else that runs code. To integrate Swift with Mesibo Objective-C SDK you need to use [Bridge Headers](https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift).

The user interface can be developed using Swift which interacts with Mesibo Native API.

The Swift portion of the app sends commands to its host to perform actions. The host is Mesibo which controls ,the iOS portion of the app, over a platform bridge.For example ,to send a message you just need to enter the access token for your application and the destination user address.
Mesibo listens and receives the information about the action to be performed. In the case of sending a message, it will receive a "Send Message" command from Swift ,upon which Mesibo calls into any number of platform-specific APIs—using the native programming language to send a message to the destination user address entered—and sends a response back to the client, the Swift portion of the app.
 
## Xamarin
Xamarin lets you build native apps for Android, iOS, and macOS using .NET code and platform-specific user interface.
Xamarin  supports the use of native libraries via the standard PInvoke mechanism. To integrate Mesibo with Xamarin refer to the documentation for integrating Native Libraries

## Ionic


