---
title: Write your First mesibo Enabled Application
description: Write your First mesibo Enabled Application
keywords: mesibo, android, ios
---
{% include_relative nav.html selected="files" %}

In this part, we will describe file transfer.

## Prerequisites
You MUST go through following prerequisites before you read further.

- Read the [Preparation]({{ '/documentation/tutorials/first-app/' | relative_url }}) Guide.
- Read one of the Android, iOS, JS section of this tutorial.

## How to add file handling

Just like text messages, mesibo allows you to send and receive any arbitrary file (image, audio, video, doc, etc) in real-time. Sending and receiving file is no different from sending and receiving text messages. All an application has to do is to first upload the file to a server and then send the URL and thumbnail [optional] using mesibo in real-time. The receiver then downloads it using that URL. This out-of-band mechanism ensures that no real-time messages are blocked waiting for large files to be uploaded or downloaded.

mesibo offers you the flexibility to store all files on your own servers including private servers or cloud services like Amazon Web Services, Google Cloud Storage, Microsoft Azure etc.

You can use any data structure (for example, json) to send rich messages which contain text message, title, picture url etc. Mesibo provides a convenient utility function sendFile, which does most of the work and allows you to send various other information along with the file such as title, message, geolocation, etc. Receiver is notified of incoming file in listener function `mesibo_onFile()`

To start a file transfer (download or upload), you must initialize a `FileInfo` object and pass it to `sendFile` API. However, instead of creating this object manually, mesibo provides `getFileInstance()` API, which checks if an existing file transfer for this particular URL (download) or the file (upload) is in progress and if so, returns that object instead of creating a new object. This highly optimizes sending a file by avoiding duplicate file transfers.

However, Mesibo does not know or mandate how and where you store your files. Hence in order to use sendFile function, you must assist mesibo in uploading and downloading files to your server. You can achieve that by implementing upload and download handler functions in `FileTransferHandler`, which is called by mesibo real-time API whenever it needs to upload or download file.  For more information, refer to `FileTransferHandler` in the [File Transfer API reference document](https://mesibo.com/documentation/api/real-time-api/file-transfer/)

![]({{ '/documentation/api/real-time-api/images/file-handler.png' | relative_url }})

### Sending a file in Android

Here is a brief set of steps you need to follow, for sending a file using mesibo on Android. We will go through each step , along with a code snippet that you can refer to as an example. You can refer [MesiboFileTransferHelper.java](https://github.com/mesibo/messenger-app-android/blob/10f7174b13c53705a257342b4d95719ff401ae9e/app/src/main/java/org/mesibo/messenger/MesiboFileTransferHelper.java) and [SampleAPI.java](https://github.com/mesibo/messenger-app-android/blob/8f40a1005e131442240963ab168f7ce260b62dfa/app/src/main/java/org/mesibo/messenger/SampleAPI.java#L666) in Messenger App Source code available on GitHub for a complete understanding.

1. To send a file, create a file object using `getFileInstance`. To create the file instance pass the mode as `MODE_UPLOAD` and the complete path to the file. Then call `sendFile` . For [example](https://github.com/mesibo/ui-modules-android/blob/72d41c5d28f5d23218f1ee77daa17a2fff86e818/Messaging/messaging/src/main/java/com/mesibo/messaging/MessagingFragment.java#L1601), 

```java
        Mesibo.FileInfo file = Mesibo.getFileInstance(mParameter, mId, Mesibo.FileInfo.MODE_UPLOAD, filetype, Mesibo.FileInfo.SOURCE_MESSAGE, filePath, null, this);
        file.message = caption;
        file.image = bmp;
        file.title = null;
        file.userInteraction = true;

        m.setFile(file);

        int sendFileresult  = Mesibo.sendFile(mParameter, mId, file);
```

2. Now, mesibo has got the path to your file. You want to upload it to your server, get a url of the location where the file can be accessed on the server and send that url in a message. So that on the other end, your recipient can access that file url, download it and view the file.

But, mesibo does not know where or how to upload it! For this you need to implement a `FileTransferHandler`
 
`FileTransferHandler` contains functions which will be called by mesibo when you start a file transfer or stop a file transfer. 
	- The function [Mesibo_onStartFileTransfer](https://github.com/mesibo/messenger-app-android/blob/10f7174b13c53705a257342b4d95719ff401ae9e/app/src/main/java/org/mesibo/messenger/MesiboFileTransferHelper.java#L220) is called when you start a file transfer.
	- The function [Mesibo_onStopFileTransfer](https://github.com/mesibo/messenger-app-android/blob/10f7174b13c53705a257342b4d95719ff401ae9e/app/src/main/java/org/mesibo/messenger/MesiboFileTransferHelper.java#L228) is called when you stop a file transfer.

3. Here is an example of how you can define `Mesibo_onStartFileTransfer`
```java
@Override
    public boolean Mesibo_onStartFileTransfer(Mesibo.FileInfo file) {
        if(Mesibo.FileInfo.MODE_DOWNLOAD == file.mode)
            return Mesibo_onStartDownload(file.getParams(), file);

        return Mesibo_onStartUpload(file.getParams(), file);
    }
``` 
`Mesibo_onStartFileTransfer` will be called with the file object you passed to `sendFile`.

When you called `getFileInstance` to create the file object, remember you can passed the  mode: `Mesibo.FileInfo.MODE_UPLOAD` to upload the file or  `Mesibo.FileInfo.MODE_DOWNLOAD` to download the file. So based on the mode, you can handle the file transfer.

For upload mode, `Mesibo_onStartUpload` is called which is your upload handler.
For download mode, `Mesibo_onStartDownload()` is called which is your download handler.
  
4. In `Mesibo_onStartUpload`, you make an HTTP request to upload your file to the server. Refer to the example code [here](https://github.com/mesibo/messenger-app-android/blob/10f7174b13c53705a257342b4d95719ff401ae9e/app/src/main/java/org/mesibo/messenger/MesiboFileTransferHelper.java#L146) to understand how this is done. Once your upload is complete and you get the fileurl, the [url parameter is set](https://github.com/mesibo/messenger-app-android/blob/10f7174b13c53705a257342b4d95719ff401ae9e/app/src/main/java/org/mesibo/messenger/MesiboFileTransferHelper.java#L116) in the file object and your file is sent.

5. On the recepient end, when a file is received, the listener `Mesibo_onFile` is called.
```java
 public void Mesibo_onFile(Mesibo.MessageParams params, Mesibo.FileInfo fileInfo) {
 }
``` 
6. Similar to the upload handler, you now need to implement the download handler to read the file data from the url present in the `FileInfo` object received. In `Mesibo_onStartDownload`, you need to download the file from the server and read the contents.  

Refer to the example code [here](https://github.com/mesibo/messenger-app-android/blob/10f7174b13c53705a257342b4d95719ff401ae9e/app/src/main/java/org/mesibo/messenger/MesiboFileTransferHelper.java#L146). Here, the file is downloaded from the server located at the file url and then stored at a specified path.


- Refer to [SampleAppFileTransferHandler.m](https://github.com/mesibo/messenger-app-ios/blob/e5af8db4061b54e135f82a2cc39549dae39494a1/MesiboApplication/SampleAppFileTransferHandler.m) (iOS) and [SampleAPI.m](https://github.com/mesibo/messenger-app-ios/blob/f89f477c687a6143b643402c70f4d61623bf2369/MesiboApplication/SampleAPI.m) in Sample app code on [GitHub](https://github.com/mesibo/samples/){:target="_blank"} to learn how to create a file handler.


[On to Part 9 >>](push-notification.md){: class="button outline-btn" style="margin-bottom: 30px; margin-right: 100%"}
