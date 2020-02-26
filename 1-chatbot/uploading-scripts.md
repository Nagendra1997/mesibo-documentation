---
description: Uploading and Configuring Scripts
keywords: chatbot, script, scripting
title: Uploading and Configuring Scripts
---
{% include_relative nav.html selected="uploading" %}

The scripting interface is provided through the Mesibo console. You need not configure or install anything additional to use the mesibo chatbot and scripting framework. Just login to your mesibo console, upload your script, and get going!

There are two parts to the process:

 1. Upload one or more scripts using the mesibo console. When you upload a script, it will get added to your script library. However, it will not be active unless you assign it to your app or one or more users. 

 2. Activate a script by assigning it to the app (all the users) or particular users.

Now, let's go in detail.

## Uploading Scripts

You can upload and manage all your scripts from your mesibo console. 

Follow the steps below to upload and manage scripts for your app.

 1. In the mesibo console, go to your app. 

 2. Click on the section `Scripts & Chatbots`. Here you will find all your uploaded scrips. 

 3. To upload a new script, Click on `Upload Script` button.

 4. Enter the description and click on the Choose File button to select and upload a script. Please ensure you are selecting a valid .js file, and the file size is less than 1 MB.

 5. That's it!

After successful upload, your script will be shown in the script library along with the Script ID and the description.

## Activating a Script
To activate a script, click on the settings icon, and it will take you to the activation screen. 

### Activating script at App Level
When you activate a script at app level, it will be executed for all the messages sent and received by the users of your application. 

To execute the script at the app level, click on the checkbox `Enable Default App Script`

### Activating script for one or more users 

You can activate a script for one or more users. You can also specify the direction (from or to) for which script should be executed. 

To activate script for a user

 1. Click on the button `Add Script User`. 

 2. Enter User ID (UID) or User Address (Name or any identifier). 

 3. Click one or more checkboxes to select whether the script should be executed when messages are received for the user OR when the user sends message OR both

 4. Save

That’s it! The script is activated, and it will be executed on the next message.









