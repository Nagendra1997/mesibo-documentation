# Mesibo Flutter Sample App
This repo contains the source code for a sample App using Flutter integrated with Mesibo.

[mesibo](https://mesibo.com) allows you to quickly add real-time messaging, voice and video calling into your mobile Apps, and Websites.
  - Enable 1-to-1 messaging, group chat, or add a chatbot in your apps or website
  - Add high quality voice chat between users
  - Adding real-time video calling in your apps
  
[Flutter](https://flutter.io/) by Google is a new framework that allows us to build beautiful native Apps on iOS and Android from a single codebase. It provides Fast Development, Expressive and Flexible UI, and Native Performance.

Mesibo provides real time APIs for messaging,voice and video call which can be easily integrated into any application on Android or iOS platforms. In this sample app, the user interface is developed using flutter which interacts with Mesibo to send messages and make audio/video calls. To achieve this we have used flutter method channels.


![flutter-mesibo-demo](mesibo-flutter.png)

- The Flutter portion of the app sends commands to  its host to perform actions.Here ,the host is Mesibo which controls ,the iOS or Android portion of the app, over a platform channel.For example ,to send a message you just need to enter the access token for your application and the destination user address.

- Mesibo listens on the platform channel, and recieves the information about the action to be performed. In the case of sending a message, it will recieve a "Send Message" command from flutter ,upon which Mesibo calls into any number of platform-specific APIs—using the native programming language to send a message to the destination user address entered—and sends a response back to the client, the Flutter portion of the app.

![flutter-mesibo-channel](https://flutter.dev/images/PlatformChannels.png)

For more information about the use  of platform channels you can refer to [flutter documentation](https://flutter.dev/docs/development/platform-integration/platform-channels)


Please check out mesibo introduction video
[![Play Video](https://img.youtube.com/vi/imHA4kBRSH0/0.jpg)](https://www.youtube.com/watch?v=imHA4kBRSH0)

Please refer to README file in each folder for more specific instructions. For general issues and help, check the FAQs section


