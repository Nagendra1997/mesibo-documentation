# Mesibo Flutter Integration Tutorial

[Mesibo](https://mesibo.com) allows you to quickly add real-time messaging, voice and video calling into your mobile Apps, and Websites.
  - Enable 1-to-1 messaging, group chat, or add a chatbot in your apps or website
  - Add high quality voice chat between users
  - Adding real-time video calling in your apps
  
[Flutter](https://flutter.io/) by Google is a new framework that allows us to build beautiful native Apps on iOS and Android from a single codebase. It provides Fast Development, Expressive and Flexible UI, and Native Performance.


Mesibo provides real time APIs for messaging,voice and video call which can be easily integrated into any application on Android or iOS platforms. Irrespective of the application platform that you are on,Mesibo can be easily integrated into any application as Mesibo provides platatform specific/Native SDK .

In this section, we will learn how to integrate flutter with Mesibo . In the sample app provides, the user interface is developed using flutter which interacts with Mesibo to send messages and make audio/video calls via method channels.

Follow the steps below to integrate Flutter with Mesibo:

### Install Android Studio

Android Studio offers a complete, integrated IDE experience for Flutter.

   - Android Studio, version 3.0 or later

Alternatively, you can also use IntelliJ:

   - IntelliJ IDEA Community, version 2017.1 or later
   - IntelliJ IDEA Ultimate, version 2017.1 or later

### Install the Flutter and Dart plugins

To install these:

   - Start Android Studio.
   - Open plugin preferences (Preferences > Plugins on macOS, File > Settings > Plugins on Windows & Linux).
   - Select Browse repositories, select the Flutter plugin and click Install.
   - Click Yes when prompted to install the Dart plugin.
   - Click Restart when prompted

### Create the app

   - Open the IDE and select Start a new Flutter project.
   - Select Flutter Application as the project type. Then click Next.
   - Verify the Flutter SDK path specifies the SDK’s location (select Install SDK… if the text field is blank).
   - Enter a project name (for example, myapp). Then click Next.
   - Click Finish.
   - Wait for Android Studio to install the SDK and create the project.
   
   
 # Add Mesibo Sdk
 
   - Add mesibo SDK to your project by adding gradle dependency and performing gradle sync as explained in our [First Android App tutorial] (https://mesibo.com/documentation/tutorials/first-app/android/)
   - Import mesibo API and add mesibo initialization code in your onCreate method

```java
import com.mesibo.api.mesibo;
```
  


- The Flutter portion of the app sends commands to  its host to perform actions.Here ,the host is Mesibo which controls ,the iOS or Android portion of the app, over a platform channel.For example ,to send a message you just need to enter the access token for your application and the destination user address.

- Mesibo listens on the platform channel, and recieves the information about the action to be performed. In the case of sending a message, it will recieve a "Send Message" command from flutter ,upon which Mesibo calls into any number of platform-specific APIs—using the native programming language to send a message to the destination user address entered—and sends a response back to the client, the Flutter portion of the app.

![flutter-mesibo-channel](https://flutter.dev/images/PlatformChannels.png)

For more information about the use  of platform channels you can refer to [flutter documentation](https://flutter.dev/docs/development/platform-integration/platform-channels)


Please check out mesibo introduction video
[![Play Video](https://img.youtube.com/vi/imHA4kBRSH0/0.jpg)](https://www.youtube.com/watch?v=imHA4kBRSH0)

Please refer to README file in each folder for more specific instructions. For general issues and help, check the FAQs section


