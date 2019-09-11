# Mesibo Swift Sample App
This repo contains the source code for a Mesibo sample App using Swift

[mesibo](https://mesibo.com) allows you to quickly add real-time messaging, voice and video calling into your mobile Apps, and Websites.
  - Enable 1-to-1 messaging, group chat, or add a chatbot in your apps or website
  - Add high quality voice chat between users
  - Adding real-time video calling in your apps

[Swift ](https://swift.org)is a fantastic way to write software, whether it’s for phones, desktops, servers, or anything else that runs code. It’s a safe, fast, and interactive programming language that combines the best in modern language thinking with wisdom from the wider Apple engineering culture and the diverse contributions from its open-source community. The compiler is optimized for performance and the language is optimized for development, without compromising on either.

The features of Swift are designed to work together to create a language that is powerful, yet fun to use. Some additional features of Swift include:

   - Closures unified with function pointers
   - Tuples and multiple return values
   - Generics
   - Fast and concise iteration over a range or collection
   - Structs that support methods, extensions, and protocols
   - Functional programming patterns, e.g., map and filter
   - Powerful error handling built-in
   - Advanced control flow with do, guard, defer, and repeat keywords


# Create a Swift Bridging Header


   - Add a new file to Xcode (File > New > File), then select “Source” and click “Header File“.
   - Name your file “YourProjectName-Bridging-Header.h”. Example: In my app Station, the file is named “Station-Bridging-Header”.
   - Create the file.
   - Navigate to your project build settings and find the “Swift Compiler – Code Generation” section. You may find it faster to type in “Swift Compiler” 
	into the search box to narrow down the results. Note: If you don’t have a “Swift Compiler – Code Generation” section, this means you probably don’t have any Swift 		classes added to your project yet. Add a Swift file, then try again.
   - Next to “Objective-C Bridging Header” you will need to add the name/path of your header file. If your file resides in your project’s root folder simply put the name of the 	header file there. Examples: “ProjectName/ProjectName-Bridging-Header.h” or simply “ProjectName-Bridging-Header.h”.
   - Open up your newly created bridging header and import your Objective-C classes using #import statements. Any class listed in this file will be able to be accessed from 		your swift classes.


Mesibo provides real time APIs for messaging,voice and video call which can be easily integrated into any application on Android or iOS platforms. In this sample app, the user interface is developed using Swift which interacts with Mesibo to send messages and make audio/video calls.

- The Swift portion of the app sends commands to  its host to perform actions.Here ,the host is Mesibo which controls ,the iOS portion of the app, over a platform bridge.For example ,to send a message you just need to enter the access token for your application and the destination user address.

- Mesibo listens and recieves the information about the action to be performed. In the case of sending a message, it will recieve a "Send Message" command from Swift ,upon which Mesibo calls into any number of platform-specific APIs—using the native programming language to send a message to the destination user address entered—and sends a response back to the client, the Swift portion of the app.



Please check out mesibo introduction video
[![Play Video](https://img.youtube.com/vi/imHA4kBRSH0/0.jpg)](https://www.youtube.com/watch?v=imHA4kBRSH0)

Please refer to README file in each folder for more specific instructions. For general issues and help, check the FAQs section

### List of Samples

* **MesiboSample** This is a sample mobile application that demonstrates how to use mesibo SDK with Swift to:
  + send and receive 1-to-1 messaging, group chat
  + make high quality voice and video calls

### Compiling sample code and setup
#### Requirements
* [mesibo account](https://mesibo.com) to get your own API KEY.
* For Android App - latest Android Studio or Gradle if you prefer CLI.
* For iOS App - latest xCode (10.x or later)
* For JavaScript Apps - [https://mesibo.com/mesiboapi.js](https://mesibo.com/mesiboapi.js)
* To install Swift [refer](https://swift.org/getting-started/#installing-swift)

### Backend Setup
Sample backend source code and database schema is available in [php](php/) folder. We recommend you to run it on your own server. However, in case you decide to use the demo API link provided in the source code (SampleAppConfiguration.java or SampleAppConfiguration.m), select a unique namespace in application configuration class to avoid conflict with other testers.

For running backend on your own server, following steps are required
* Create your own mesibo account to get mesibo API key and App token. You may also read [tutorial](https://mesibo.com/documentation/tutorials.html) on how to get mesibo API key and App token.
* Create database and table using schema in [php/SampleApp.sql](https://github.com/mesibo/samples/blob/master/php/sample-app.sql)
* edit config.php and enter API Key, App token, and database credentials.
* change apiUrl in respective application configuration classes.

# Mesibo Swift Sample App Source Code
This repository has contains the sample app that demonstrate various aspects of the mesibo SDK for iOS with Swift. The sample code is reasonably well-documented and we suggest you to read comments to quickly understand the code. The entire documentation for the mesibo SDK is available [here](https://mesibo.com/documentation/introduction.html).

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
