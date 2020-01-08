## Everything wrong with using Telegram to build your application

Telegram is a great platform for secure communication.  With major communication tools like Whatsapp, Messenger, etc being compromised it is truly remarkable that Telegram- a free, cloud based platform with strong encryption has stood its ground.

But, is it really as strong as it sounds? Does it make sense to use Telegram APIs to build your application? 

The answer? A big NO.
Here’s why.
 
### Telegram is not an SDK
Telegram is not really an SDK or API platform. Although the Telegram messaging application on it’s own is a well built tool, it offers nothing to extend or replicate the same into your application. The API is far from complete and offers limited support to custom features.

Telegram have only put all of their code out in the open and expect you to figure it out on how to use it.
You have to put in  a lot of effort and time in “figuring out” . You also need to publish this code under an open-source licence that you worked hard to create. This is because Telegram offers a BSD Style Licence and any software built using the Telegram source  must be distributed under the same licence. 

### You cannot independently brand your application
Telegram is an open-source application. According to the licence of Telegram using the source code of Telegram come with a set of caveats. One of them is you cannot rebrand it completely as your own app. It needs to be prominently disclosed that your app is powered over Telegram.

Another issue is with the Telegram Metadata. When you build your application with Telegram, it in turn syncs up with the Telegram account linked with the user and other Telegram Metadata. But, this is not an optional step. Your application cannot be built as a standalone entity. The only thing Telegram APIs allow you to do is to interface with their own data and quirks.

Say you build a messaging application using Telegram. You will need to authenticate login with the phone number linked with a Telegram Account. That is, all the profiles of your users are linked with their respective Telegram Accounts and your application cannot exist without it.

### No option to host the application on your own premise 
For instance, if you wish to interface with private data on a private network and you need to build something like a chatbot that interacts with sensitive user data, it is a bad decision to trust a third party cloud service.

Using Telegram to build your application means that you connect with Telegram Cloud Servers. 
If you wish to host Telegram on your own servers it is an absolute no go.

### Not an extendable code base
The Telegram source is mostly in C , with a lot of hard-coded values and no system constants. There is a LOT of spaghetti code with unnecessarily long methods with nonexistent documentation. Moreover, there is no reliable extension API to build on top of the base platform. You will need to spend a lot of time understanding the complicated architecture to add or modify a new feature.

The source code is not reusable at all and you may end up spending a lot of time understanding it before you dive
into building your own application.

Is there a better solution? The answer is  Mesibo.

## Mesibo's Real-Time Communication SDK
Mesibo is built as a full-fledged real time SDK that lets you quickly add communication capabilities to your apps for Mobile, Web and IoT. The SDK is available for Android, iOS, Web, Linux and many more popular platforms. There is no restriction whatesoever in using Mesibo Source Code in your application. Meisbo is *built by developers for developers*. The APIs are designed to be consistent across all platforms in a developer friendly manner with rich documentation. Mesibo allows you to cut your development time in half by providing everything you need to build applications that are highly scalable and robust.

### Build independent applications with your own brand
You can customize each and every element of your application as you wish under your own branding. Mesibo offers you an Apache Style Licence. This means you need not open source your application if you do not wish to. 
You can rebrand the application as you seem fit, with your name and colors. You can use Mesibo's Open Source UI Modules for [iOS](https://github.com/mesibo/ui-modules-ios) and [Android](https://github.com/mesibo/ui-modules-ios
) to develop a fully customised Mobile Application with all the features of Mesibo.

### Deploy your application on your own premise
While Mesibo offers you a cloud based solution that allows you to host your application on Mesibo Servers to quickly get started, it also allows you to host the application on your own datacenter/private cloud. You can interface with your private data in a secure manner without compromise. With [Mesibo On-Premise](https://mesibo.com/on-premise/) you can fully configure your backend as per your service requirements and scale at ease.

### A powerful backend with Loadable Modules and Scripting 
Mesibo allows you to extend the features and capabilities of the core platform with a loadable Module Architecture. With [Mesibo On-Premise Loadable Modules](https://mesibo.com/documentation/on-premise/loadable-modules/) you can add custom features to Mesibo and integrate more tightly with your application backend. You can interface with private data and secure connections without breaking a sweat. You can build powerful communication applications like chatbots, translators and much more.

With the Scripting Module you can implement your application logic over a simple Javascript interface without compromising on performance.



